<p align="center">
  <h3 align="center">Teste: Infrastructure-as-Code</h3>
</p>

<details open="open">
  <summary>Tópicos</summary>
  <ol>
    <li>
      <a href="#resumo">Resumo</a>
    </li>
    <li>
      <a href="#implementação-com-virtualbox">Implementação com VirtualBox</a>
      <ul>
        <li><a href="#pré-requisitos">Pré-requisitos</a></li>
        <li><a href="#instalação">Instalação</a></li>
      </ul>
    </li>
    <li>
      <a href="#implementação-no-azure">Implementação no Azure</a>
      <ul>
        <li><a href="#pré-requisitos-azure">Pré-requisitos</a></li>
        <li><a href="#instalação-azure">Instalação</a></li>
      </ul>
     <li><a href="#validação">Validação</a></li>
  </ol>
</details>

## Resumo

O objetivo é a utilização de alguma ferramenta de Infrastructure-As-Code para a criação e configuração de VMs automaticamente.

* Criação de VM de forma automatizada:
  * SO: CentOS Linux 8 x86_64
  * Softwares:
    * VIM
    * Oracle Java SE 16
* Variável de ambiente: NUMERO_DA_SORTE=777
* Criar 3 VMs com IPs únicos

## Implementação com VirtualBox

### Pré-requisitos

As seguintes ferramentas foram utilizadas:

* [Vagrant](https://www.vagrantup.com/downloads)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

### Instalação

1. Clone o repositório

   ```sh
   git clone https://github.com/santosmarcelob/teste-iac.git
   ```

2. Por padrão, serão criadas 3 VMs, para alterar a quantidade de VMs, edite o valor da variável **vms** no arquivo Vagrantfile. 

   ```ruby
   vms = 3 
   ```

3. Dentro da pasta **teste-IaC**, execute `vagrant up` e o vagrant iniciará a criação das VMs. 

   ```powershell
   PS C:\teste-IaC> vagrant up
   Bringing machine 'host1' up with 'virtualbox' provider...
   Bringing machine 'host2' up with 'virtualbox' provider...
   Bringing machine 'host3' up with 'virtualbox' provider...
   ```

   Após alguns minutos, as máquinas estarão criadas.

   ```powershell
   PS C:\teste-IaC> vagrant status
   Current machine states:
   
   host1                     running (virtualbox)
   host2                     running (virtualbox)
   host3                     running (virtualbox)
   ```

   Os IPs serão iniciados em ***172.16.5.2 (host1)*** e incrementarão conforme a quantidade de máquinas.

   ```powershell
   PS C:\teste-IaC> vagrant ssh host1 -c "ip a | grep 172"
       inet 172.16.5.2/24 brd 172.16.5.255 scope global noprefixroute eth1
   Connection to 127.0.0.1 closed.
   PS C:\teste-IaC> vagrant ssh host2 -c "ip a | grep 172"
       inet 172.16.5.3/24 brd 172.16.5.255 scope global noprefixroute eth1
   Connection to 127.0.0.1 closed.
   PS C:\teste-IaC> vagrant ssh host3 -c "ip a | grep 172"
       inet 172.16.5.4/24 brd 172.16.5.255 scope global noprefixroute eth1
   Connection to 127.0.0.1 closed.
   ```

4. Para acessar a máquina, execute `vagrant ssh host1`:

   ```powershell
   PS C:\teste-IaC> vagrant ssh host1
   [vagrant@host1 ~]$
   ```

   Para acessar as outras VMs, altere **"host1"** pelo respectivo *hostname* da VM.

## Implementação no Azure

Também é possível utilizar esta solução para deploy de máquinas em nuvem. A cloud Azure foi utilizada nesta implementação.

### Pré-requisitos Azure

As seguintes ferramentas foram utilizadas:

* [Vagrant](https://www.vagrantup.com/downloads)
* [Conta Microsoft Azure](https://azure.microsoft.com/pt-br/free/)

### Instalação Azure

1. Clone o repositório

   ```sh
   git clone https://github.com/santosmarcelob/teste-iac.git
   ```

2. Instale o [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)

3. Faça o login no Azure com `az login`

4. execute `az ad sp create-for-rbac` para criar uma aplicação AD com acesso ao Azure Resource Manager

   ```powershell
   {
     "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
     "displayName": "some-display-name",
     "name": "http://azure-cli-2017-04-03-15-30-52",
     "password": "XXXXXXXXXXXXXXXXXXXX",
     "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
   }
   ```

5. Utilize o arquivo "**Vagrantfile**" localizado na pasta "Azure". Os valores `tenant`, `appId` e`password` deverão ser inseridos em `azure.tenant_id`, `azure.client_id` e`azure.client_secret` no arquivo Vagrantfile.

   ```ruby
   azure.tenant_id = 'AZURE_TENANT_ID'
   azure.client_id = 'AZURE_CLIENT_ID'
   azure.client_secret = 'AZURE_CLIENT_SECRET'
   azure.subscription_id = 'AZURE_SUBSCRIPTION_ID'
   ```

6. Por padrão, serão criadas 3 VMs, para alterar a quantidade de VMs, edite o valor da variável **vms** no arquivo Vagrantfile. 

   ```ruby
   vms = 3 
   ```

7. Crie uma pasta .ssh e gere uma chave SSH Key nesta pasta.

   ```powershell
   PS C:\vagrant-azure> ssh-keygen
   Generating public/private rsa key pair.
   Enter file in which to save the key (C:\Users\santo/.ssh/id_rsa): C:\vagrant-azure/.ssh/id_rsa
   Enter passphrase (empty for no passphrase):
   Enter same passphrase again:
   Your identification has been saved in C:\vagrant-azure/.ssh/id_rsa.
   Your public key has been saved in C:\vagrant-azure/.ssh/id_rsa.pub.
   ```
   
8. Instale o plugin **vagrant-azure**, e execute `vagrant up --provider=azure` para fazer o deploy

   ```powershell
   PS C:\vagrant-azure> vagrant plugin install vagrant-azure
   Installing the 'vagrant-azure' plugin. This can take a few minutes...
   ```

   ```powershell
   PS C:\vagrant-azure> vagrant up --provider=azure
   Bringing machine 'host1' up with 'azure' provider...
   Bringing machine 'host2' up with 'azure' provider...
   Bringing machine 'host3' up with 'azure' provider...
   ```

9. Após o deploy estar completo será possível acessar as máquinas via ssh ou diretamente no Azure Portal.

   ```powershell
   PS C:\vagrant-azure> vagrant status
   Current machine states:
   
   host1                     running (azure)
   host2                     running (azure)
   host3                     running (azure)
   ```

   ```powershell
   PS C:\vagrant-azure> vagrant ssh host1 -c "hostnamectl"
      Static hostname: floral-glade-70
            Icon name: computer-vm
              Chassis: vm
           Machine ID: 6cc37d7585ce476d8909603a266fb56f
              Boot ID: 95ecf6e796e543f0a2291be869398e9c
       Virtualization: microsoft
     Operating System: CentOS Linux 8 (Core)
          CPE OS Name: cpe:/o:centos:centos:8
               Kernel: Linux 4.18.0-193.28.1.el8_2.x86_64
         Architecture: x86-64
   Connection to floral-glade-70.westus.cloudapp.azure.com closed.
   PS C:\vagrant-azure> vagrant ssh host2 -c "hostnamectl"
      Static hostname: winter-sea-51
            Icon name: computer-vm
              Chassis: vm
           Machine ID: 6cc37d7585ce476d8909603a266fb56f
              Boot ID: 6f2977082704480a9d7ccdca1e2803c2
       Virtualization: microsoft
     Operating System: CentOS Linux 8 (Core)
          CPE OS Name: cpe:/o:centos:centos:8
               Kernel: Linux 4.18.0-193.28.1.el8_2.x86_64
         Architecture: x86-64
   Connection to winter-sea-51.westus.cloudapp.azure.com closed.
   ```

10. Para acessar a máquina, execute `vagrant ssh host1`:

    ```powershell
    PS C:\vagrant-azure> vagrant ssh host1
    [vagrant@floral-glade-70 ~]$
    ```

    Para acessar as outras VMs, altere **"host1"** pelo respectivo *hostname* da VM.

## Validação

A validação das configurações foi efetuada em todas as VMs criadas:

1. Sistema Operacional: **CentOS Linux 8 x86_64**

   ```bash
   [vagrant@host1 ~]$ hostnamectl
      Static hostname: host1
            Icon name: computer-vm
              Chassis: vm
           Machine ID: cfaacb3a55c34f6f90b6df14c69ede75
              Boot ID: 01ca1a023e764dcda2e2b7ca4b81da0e
       Virtualization: oracle
     Operating System: CentOS Linux 8
          CPE OS Name: cpe:/o:centos:centos:8
               Kernel: Linux 4.18.0-240.1.1.el8_3.x86_64
         Architecture: x86-64 
   ```

2. Software: **VIM**

   ```bash
   [vagrant@host1 ~]$ vim -version
   VIM - Vi IMproved 8.0 (2016 Sep 12, compiled Jun 18 2020 15:49:08)
   Garbage after option argument: "-version"
   More info with: "vim -h"
   ```

3. Software: **Oracle Java SE 16 default**

   ```bash
   [vagrant@host1 ~]$ java -version
   java version "16.0.1" 2021-04-20
   Java(TM) SE Runtime Environment (build 16.0.1+9-24)
   Java HotSpot(TM) 64-Bit Server VM (build 16.0.1+9-24, mixed mode, sharing)
   ```

   ```bash
   [vagrant@host1 ~]$ echo $JAVA_HOME
   /usr/java/jdk-16.0.1/bin/java
   ```

4. Variável de ambiente: **NUMERO_DA_SORTE=777**

   ```bash
   [vagrant@host1 ~]$ echo $NUMERO_DA_SORTE
   777
   ```

   ```bash
   [vagrant@host1 ~]$ printenv
   LS_COLORS=rs=0:di=38;5;33:ln=38;5;51:mh=00:pi=40;38;5;11:so=38;5;13:do=38;5;5:bd=48;5;232;38;5;11:cd=48;5;232;38;5;3:or=48;5;232;38;5;9:mi=01;05;37;41:su=48;5;196;38;5;15:sg=48;5;11;38;5;16:ca=48;5;196;38;5;226:tw=48;5;10;38;5;16:ow=48;5;10;38;5;21:st=48;5;21;38;5;15:ex=38;5;40:*.tar=38;5;9:*.tgz=38;5;9:*.arc=38;5;9:*.arj=38;5;9:*.taz=38;5;9:*.lha=38;5;9:*.lz4=38;5;9:*.lzh=38;5;9:*.lzma=38;5;9:*.tlz=38;5;9:*.txz=38;5;9:*.tzo=38;5;9:*.t7z=38;5;9:*.zip=38;5;9:*.z=38;5;9:*.dz=38;5;9:*.gz=38;5;9:*.lrz=38;5;9:*.lz=38;5;9:*.lzo=38;5;9:*.xz=38;5;9:*.zst=38;5;9:*.tzst=38;5;9:*.bz2=38;5;9:*.bz=38;5;9:*.tbz=38;5;9:*.tbz2=38;5;9:*.tz=38;5;9:*.deb=38;5;9:*.rpm=38;5;9:*.jar=38;5;9:*.war=38;5;9:*.ear=38;5;9:*.sar=38;5;9:*.rar=38;5;9:*.alz=38;5;9:*.ace=38;5;9:*.zoo=38;5;9:*.cpio=38;5;9:*.7z=38;5;9:*.rz=38;5;9:*.cab=38;5;9:*.wim=38;5;9:*.swm=38;5;9:*.dwm=38;5;9:*.esd=38;5;9:*.jpg=38;5;13:*.jpeg=38;5;13:*.mjpg=38;5;13:*.mjpeg=38;5;13:*.gif=38;5;13:*.bmp=38;5;13:*.pbm=38;5;13:*.pgm=38;5;13:*.ppm=38;5;13:*.tga=38;5;13:*.xbm=38;5;13:*.xpm=38;5;13:*.tif=38;5;13:*.tiff=38;5;13:*.png=38;5;13:*.svg=38;5;13:*.svgz=38;5;13:*.mng=38;5;13:*.pcx=38;5;13:*.mov=38;5;13:*.mpg=38;5;13:*.mpeg=38;5;13:*.m2v=38;5;13:*.mkv=38;5;13:*.webm=38;5;13:*.ogm=38;5;13:*.mp4=38;5;13:*.m4v=38;5;13:*.mp4v=38;5;13:*.vob=38;5;13:*.qt=38;5;13:*.nuv=38;5;13:*.wmv=38;5;13:*.asf=38;5;13:*.rm=38;5;13:*.rmvb=38;5;13:*.flc=38;5;13:*.avi=38;5;13:*.fli=38;5;13:*.flv=38;5;13:*.gl=38;5;13:*.dl=38;5;13:*.xcf=38;5;13:*.xwd=38;5;13:*.yuv=38;5;13:*.cgm=38;5;13:*.emf=38;5;13:*.ogv=38;5;13:*.ogx=38;5;13:*.aac=38;5;45:*.au=38;5;45:*.flac=38;5;45:*.m4a=38;5;45:*.mid=38;5;45:*.midi=38;5;45:*.mka=38;5;45:*.mp3=38;5;45:*.mpc=38;5;45:*.ogg=38;5;45:*.ra=38;5;45:*.wav=38;5;45:*.oga=38;5;45:*.opus=38;5;45:*.spx=38;5;45:*.xspf=38;5;45:
   SSH_CONNECTION=10.0.2.2 51972 10.0.2.15 22
   LANG=en_US.UTF-8
   HISTCONTROL=ignoredups
   HOSTNAME=host1
   JAVA_HOME=/usr/java/jdk-16.0.1/bin/java
   XDG_SESSION_ID=3
   NUMERO_DA_SORTE=777
   USER=vagrant
   SELINUX_ROLE_REQUESTED=
   PWD=/home/vagrant
   HOME=/home/vagrant
   SSH_CLIENT=10.0.2.2 51972 22
   SELINUX_LEVEL_REQUESTED=
   SSH_TTY=/dev/pts/0
   MAIL=/var/spool/mail/vagrant
   TERM=xterm-256color
   SHELL=/bin/bash
   SELINUX_USE_CURRENT_RANGE=
   SHLVL=1
   LOGNAME=vagrant
   DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
   XDG_RUNTIME_DIR=/run/user/1000
   PATH=/home/vagrant/.local/bin:/home/vagrant/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin
   HISTSIZE=1000
   LESSOPEN=||/usr/bin/lesspipe.sh %s
   _=/usr/bin/printenv
   ```

   
   
   