# TP 5 - Admin Système
## Shana et Pierrick

----
# Exercice 1. Adressage IP


_Réseau interne 172.16.0.0/23 d’une entreprise, avec un parc de 254 machines réparties en 7 sous-réseaux. 8 = 2^3 donc on utilise un masque 26. On doit selectionner des multiples de 2. On callibrera donc 64 hotes possibles au maximum par sous réseau **(car 32< 34 < 64 )** La répartition des machines est la suivante :_
- _Sous-réseau 1 : 38 machines_  
Addresse de sous réseau : 172.16.0.0   
Adresse de broadcast :  172.16.0.63  
Première machine configuréé : 172.16.0.1   
Dernière machine configurée : 172.16.0.62  

- _Sous-réseau 2 : 33 machines_   
Addresse de sous réseau : 172.16.0.64  
Adresse de broadcast : 172.16.0.127  
Première machine configuréé : 172.16.0.65  
Dernière machine configurée : 172.16.0.126     

- _Sous-réseau 3 : 52 machines_    
Addresse de sous réseau : 172.16.0.128   
Adresse de broadcast :  172.16.0.191  
Première machine configuréé : 172.16.0.129   
Dernière machine configurée : 172.16.0.190   

- _Sous-réseau 4 : 35 machines_   
Addresse de sous réseau : 172.16.0.192  
Adresse de broadcast : 172.16.0.255     
Première machine configuréé : 172.16.0.193     
Dernière machine configurée : 172.16.0.254   

- _Sous-réseau 5 : 34 machines_   
Addresse de sous réseau :  172.16.1.0  
Adresse de broadcast :  172.16.1.63    
Première machine configuréé : 172.16.1.1        
Dernière machine configurée : 172.16.1.62     

- _Sous-réseau 6 : 37 machines_    
Addresse de sous réseau : 172.16.1.64  
Adresse de broadcast : 172.16.1.127    
Première machine configuréé : 172.16.1.65      
Dernière machine configurée :172.16.1.126    

- _Sous-réseau 7 : 25 machines_   
Addresse de sous réseau : 172.16.1.128  
Adresse de broadcast :  172.16.1.255  
Première machine configuréé :172.16.1.193     
Dernière machine configurée :172.16.1.254   

L'adressage ne nécessite qu'un adresse IP pour le découpage et le nombre d'hote ne depasse pas 54 en un premier temps donc le masque 26 convient bien à la demande. 
On permet même une marge dans le cas d'ajout d'hotes sur le réseau. 

----

# Exercice 2. Préparation de l’environnement

### .1 __
### .2 Que représente l'interface `lo`


L'interface `lo` représente l'adresse de loopback. Une loopback est une interface virtuelle d'un matériel réseau (ou d'un ordinateur), et par extension une adresse associée à cette interface. Ainsi, quand il la contacte il "boucle" sur lui-même.

----
# Exercice 3. Installation du serveur DHCP

2. _UnserveurDHCPabesoind’uneIPstatique.Attribuezdemanièrepermanentel’adresseIP192.168.100.1 à l’interface réseau du réseau interne. Vérifiez que la configuration est correcte._

On utilise `netplan` en écrivant dans le dossier de configuration `/etc/netplan/`

```
network:
    version: 2
    renderer: NetworkManager (ou networkd)

    ethernets:
        enp0s8:
            addresses:
                - 192.168.100.1/24
```
On peut verifier grâce à la commande `ip a`
`netplan`permet que l'attribution de l'addresse Ip soit permanente (contrairement à `ifconfig`)

3. La configuration du serveur DHCP

```
default-lease-time 120;
max-lease-time 600;
```
Ces lignes servent à faire un reload du serveur fréquement.

4. Editez le fichier /etc/default/isc-dhcp-server afin de spécifier l’interface sur laquelle le serveur doit écouter.

7. Reaction du serveur à la connection du client 

Activer une interface : `ip link set enp0s3 up`

pour que ça marche on doit générer uen ip automatique avec dhclient enp0s3

On recoit sur le terminal serveur   
`DHCPDISCOVER` où il découvre   l'adresse MAC du client     
`DHCPOFFER` où il offre une adresse de réseau à l'adresse mac précédemment reconnue  
`DHCPREQUEST` on recoit une demande du client via sa nouvelle réseau   `DHCPACK` est un envoit de packet sur l'adresse de réseau 


8. 
Le _fichier/var/lib/dhcp/dhcpd.leases_ sur le serveur indique le spermission de connection et les connections effectuées sur le serveur en temps et en heure   
 `dhcp-lease-list`
Donne une liste des appareils s'étant connectés au serveur avec leur adresse MAC, IP, leur nom de machine et leur horaire de connection.

9. 
On realise un `ping` sur l'adresse IP du client (ici **192.168.100.100**) et un `ping`sur l'adresse de réseau du serveur **192.168.100.0**




----
client se connecte au net en passant par le serveur : `iptables -t nat -a POSTROUTING -s 192.168.1.0/24 -o enp0s3 -j MASQUERADE`
