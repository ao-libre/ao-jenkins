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

## Links de interes:
- http://zbz5.net/automating-build-visual-basic-6-project
- https://github.com/jenkinsci/visual-basic-6-plugin
