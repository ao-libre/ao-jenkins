# ao-jenkins
Configuration necessary to use jenkins with Argentum Online Libre Server

1- Instalar GIT desde https://git-scm.com/

2- Instalar Visual Studio 6 - El instalador se encuentra en este repositorio

3- Instalar Jenkins desde https://jenkins.io/ con configuracion recomendada

4- Instalar Visual Basic 6 Plugin para Jenkins (https://github.com/jenkinsci/visual-basic-6-plugin)

5- Instalar Discord Notifier (https://plugins.jenkins.io/discord-notifier/)

6- Configurar Jenkins como se muestra en la siguiente imagen

![imagen](https://github.com/ao-libre/ao-jenkins/blob/master/jenkins-config.png)


System Management:

![imagen](https://raw.githubusercontent.com/ao-libre/ao-jenkins/master/jenkins%20vb6.png)

Path VBP
```
C:\Program Files (x86)\Jenkins\workspace\ao-server-master\SERVER.VBP
```



# MASTER (SERVER TEST)

Windows Batch
```
echo "Borramos archivos innecesarios"
rmdir /s /q "C:\Program Files (x86)\Jenkins\workspace\ao-server-master\.git" 
rmdir /s /q "C:\Program Files (x86)\Jenkins\workspace\ao-server-master\.github"
rmdir /s /q "C:\Program Files (x86)\Jenkins\workspace\ao-server-master\Codigo"
del /f  "C:\Program Files (x86)\Jenkins\workspace\ao-server-master\.gitignore"
del /f  "C:\Program Files (x86)\Jenkins\workspace\ao-server-master\.gitattributes"
del /f  "C:\Program Files (x86)\Jenkins\workspace\ao-server-master\SERVER.VBP"

echo "Poniendo contenido en la carpeta del server de produccion"
mkdir "C:\ao-server-master" 
xcopy /Y /E "C:\Program Files (x86)\Jenkins\workspace\ao-server-master" "C:\ao-server-master"
```

# Server Principal, Produccion

Windows batch
```
echo "Hacemos esto para no perder la carpeta .git y despues que la shell pueda hacer el release a github"
mkdir "C:\ao-server-release-without-rubbish-files-temp"

xcopy /Y /E "C:\Program Files (x86)\Jenkins\workspace\ao-server-release" "C:\ao-server-release-without-rubbish-files-temp"

echo "Borramos archivos innecesarios"
rmdir /s /q "C:\ao-server-release-without-rubbish-files-temp\.git" 
rmdir /s /q "C:\ao-server-release-without-rubbish-files-temp\.github"
rmdir /s /q "C:\ao-server-release-without-rubbish-files-temp\Codigo"
del /f  "C:\ao-server-release-without-rubbish-files-temp\.gitignore"
del /f  "C:\ao-server-release-without-rubbish-files-temp\.gitattributes"
del /f  "C:\ao-server-release-without-rubbish-files-temp\SERVER.VBP"

echo "Poniendo contenido en la carpeta del server de produccion"
mkdir "C:\ao-server-release" 
xcopy /Y /E "C:\ao-server-release-without-rubbish-files-temp" "C:\ao-server-release"

echo "Creamos .zip para poder subirlo a github como asset de release"
powershell Compress-Archive -Path "C:\ao-server-release-without-rubbish-files-temp" artifact.zip
```

SHELL
```
# Build

# Publish on github
echo "<<< Publishing on AO-LIBRE Server Release  >>>"
token="TOKEN"

# Get the last tag name
tag=$(git describe --tags)

# Get the full message associated with this tag
#message="$(!git pull; git log --pretty=format:'%h - %an, %ad : %s' $(git tag -l | grep v | sort | tail -n 1)...origin/master)"
message="Testing v0.13.29"


# Get the title and the description as separated variables
name=$tag
description=$(echo "$message")
description=$(echo "$description" | sed -z 's/\n/\\n/g') # Escape line breaks to prevent json parsing problems

# Create a release
release=$(curl -XPOST -H "Authorization:token $token" --data "{\"tag_name\": \"$tag\", \"target_commitish\": \"master\", \"name\": \"$name\", \"body\": \"$description\", \"draft\": false, \"prerelease\": false}" https://api.github.com/repos/ao-libre/ao-server/releases)

# Extract the id of the release from the creation response
id=$(echo "$release" | sed -n -e 's/"id":\ \([0-9]\+\),/\1/p' | head -n 1 | sed 's/[[:blank:]]//g')

# Upload the artifact
curl -XPOST -H "Authorization:token $token" -H "Content-Type:application/octet-stream" --data-binary @artifact.zip https://uploads.github.com/repos/ao-libre/ao-server/releases/$id/assets?name=$tag.zip
```

WINDOWS BATCH
```
echo "Borramos carpeta temporal"
rmdir /s /q "C:\ao-server-release-without-rubbish-files-temp"
```

# Cliente Release

WINDOWS BATCH
```
echo "Hacemos esto para no perder la carpeta .git y despues que la shell pueda hacer el release a github"
mkdir "C:\ao-cliente-release-without-rubbish-files-temp"

xcopy /Y /E "C:\Program Files (x86)\Jenkins\workspace\ao-cliente-release" "C:\ao-cliente-release-without-rubbish-files-temp"

echo "Borramos archivos innecesarios"
rmdir /s /q "C:\ao-cliente-release-without-rubbish-files-temp\.git" 
rmdir /s /q "C:\ao-cliente-release-without-rubbish-files-temp\.github"
rmdir /s /q "C:\ao-cliente-release-without-rubbish-files-temp\CODIGO"
del /f  "C:\ao-cliente-release-without-rubbish-files-temp\.gitignore"
del /f  "C:\ao-cliente-release-without-rubbish-files-temp\.gitattributes"
del /f  "C:\ao-cliente-release-without-rubbish-files-temp\Client.vbp"

echo "Creamos .zip para poder subirlo a github como asset de release"
powershell Compress-Archive -Path "C:\ao-cliente-release-without-rubbish-files-temp" artifact.zip
```

SHELL
```
# Build

# Publish on github
echo "<<< Publishing on AO-LIBRE Cliente Release  >>>"
token="TOKEN"

# Get the last tag name
tag=$(git describe --tags)

# Get the full message associated with this tag
#message="$(!git pull; git log --pretty=format:'%h - %an, %ad : %s' $(git tag -l | grep v | sort | tail -n 1)...origin/master)"
message="Testing v0.13.29"


# Get the title and the description as separated variables
name=$tag
description=$(echo "$message")
description=$(echo "$description" | sed -z 's/\n/\\n/g') # Escape line breaks to prevent json parsing problems

# Create a release
release=$(curl -XPOST -H "Authorization:token $token" --data "{\"tag_name\": \"$tag\", \"target_commitish\": \"master\", \"name\": \"$name\", \"body\": \"$description\", \"draft\": false, \"prerelease\": false}" https://api.github.com/repos/ao-libre/ao-cliente/releases)

# Extract the id of the release from the creation response
id=$(echo "$release" | sed -n -e 's/"id":\ \([0-9]\+\),/\1/p' | head -n 1 | sed 's/[[:blank:]]//g')

# Upload the artifact
curl -XPOST -H "Authorization:token $token" -H "Content-Type:application/octet-stream" --data-binary @artifact.zip https://uploads.github.com/repos/ao-libre/ao-cliente/releases/$id/assets?name=$tag.zip
```

WINDOWS BATCH
```
echo "Borramos carpeta temporal"
rmdir /s /q "C:\ao-cliente-release-without-rubbish-files-temp"
```

## Links de interes:
- http://zbz5.net/automating-build-visual-basic-6-project
- https://github.com/jenkinsci/visual-basic-6-plugin
