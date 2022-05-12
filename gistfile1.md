#Gitlab mit CI und Container-Registry (Pipeline für chatbot_docker)

#1. Vorbereitungen

Windows-Benutzer nutzen bitte eine VM mit Ubuntu 20.04 und nested Virtualization

sudo apt update  && sudo apt -y install build-essential vagrant git virtualbox
vagrant plugin install vagrant-vbguest
sudo adduser $USER vboxusers
[ -d /etc/vbox ] || sudo mkdir -p /etc/vbox
echo "* 192.168.1.0/24" | sudo tee -a /etc/vbox/networks.conf
echo "192.168.1.11 gitlab.kurs.iad gitlab" | sudo tee -a /etc/hosts
mkdir -p $HOME/Dokumente/git
cd $HOME/Dokumente/git

#2. Gitlab

git clone https://github.com/JoergEF/Gitlab-Docker-standalone.git
cd Gitlab-Docker-standalone
nano -l scripts/bootstrap.sh
  --> in Zeile 13 eigene Nameserver-IP eintragen und speichern
make

Initiales root-Passwort anzeigen

vagrant ssh
sudo grep 'Password:' /srv/gitlab/config/initial_root_password

Website aufrufen (dauert ca.25 min.)

    https://gitlab/

#3. Gitlab Runner

cd $HOME/Dokumente/git
git clone https://github.com/JoergEF/Gitlab-Runner-mit-Docker.git
cd Gitlab-Runner-mit-Docker
make

#4. anschließende Tätigkeiten

    root-Passwort ändern (Datei mit Passwort wird nach 24 Stunden gelöscht)

    neuen Benutzer anlegen

    Passwort ändern

    Ummelden und Passwort bestätigen

    neues Projekt anlegen/importieren

    2x Runner registrieren

    vagrant ssh
    sudo gitlab-runner register

     Gitlab-Instance:    https://gitlab/
     Registration Token: (aus Gitlab-WebGUI)
     Description:        <ENTER>
     Tags:               linux,ubuntu,shell,python
     Maintenance Note:   <ENTER>
     Executor:           shell

    sudo gitlab-runner register

     Gitlab-Instance:    https://gitlab/
     Registration Token: (aus Gitlab-WebGUI)
     Description:        docker-runner
     Tags:               linux,ubuntu,docker
     Maintenance Note:   <ENTER>
     Executor:           docker
     Default Image:      ruby:2.7

    Pipeline ausführen

    Docker-VM installieren

    cd $HOME/Dokumente/git
    git clone https://github.com/JoergEF/Gitlab-Docker-mini.git
    cd Gitlab-Docker-mini
    make

    Container laden (in der Docker-VM):

    docker run -d -p 5000:5000 gitlab:5050/chatbot/chatbot-docker/main:latest

