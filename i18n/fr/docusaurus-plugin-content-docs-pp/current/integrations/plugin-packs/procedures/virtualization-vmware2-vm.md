---
id: virtualization-vmware2-vm
title: VMware VM
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## Vue d'ensemble

VMWare est une solution de Virtualisation et d'infrastructure de Cloud Computing.

Le connecteur de supervision Centreon s'appuie sur le SDK VMWare pour requêter l'API du vCenter au travers d'un connecteur dédié. 

> Ce connecteur de supervision fonctionne avec les versions 6 et 7 de l'API VMware.


## Contenu du connecteur de supervision

### Objets supervisés

* Virtual Machines

### Règles de découvertes

<Tabs groupId="sync">
<TabItem value="Hosts" label="Hosts">

| Rule name                      | Description                                                       |
| :----------------------------- | :---------------------------------------------------------------- |
|  Virt-VMWare2-VM-HostDiscovery | Découvrez vos Machines Virtuelles liées à un vCenter ou à un ESXi |

</TabItem>
</Tabs>

### Métriques Collectées

En plus des modes et des métriques détaillées ci-après, il est également possible de superviser les éléments suivants :

* VM-Cpu : Supervision avancée du CPU (CPU ready)
* VM-Datastores-Iops : Utilisation de la VM en Iops sur les Datastores
* VM-Swap : Utilisation du swap de la VM
* VM-Device : Contrôle un périphérique d'une VM (Par exemple VirtualCdrom)

<Tabs groupId="sync">
<TabItem value="Vm-Tools" label="Vm-Tools">

| Metric name | Description                                                    | Unit |
| :---------- | :------------------------------------------------------------- | :--- |
| status      | Status of the VMware Tools (installed, running and up-to-date) |      |

</TabItem>
<TabItem value="Vm-Thinprovisioning" label="Vm-Thinprovisioning">

| Metric name | Description                                | Unit |
| :---------- | :----------------------------------------- | :--- |
| status      | Status of the Thinprovisoning virtualdisks |      |

</TabItem>
<TabItem value="Vm-Status" label="Vm-Status">

| Metric name   | Description              | Unit |
| :------------ | :----------------------- | :--- |
| status        | Overall status of the VM |      |

</TabItem>
<TabItem value="Vm-Snapshot" label="Vm-Snapshot">

| Metric name                         | Description                                                   | Unit  |
| :---------------------------------- | :------------------------------------------------------------ | :---- |
| vm.snapshots.warning.current.count  | Number of VM's snapshot older than 3 days (default threshold) | Count |
| vm.snapshots.critical.current.count | Number of VM's snapshot older than 5 days (default threshold) | Count |

</TabItem>
<TabItem value="Vm-Limit" label="Vm-Limit">

| Metric name                  | Description            | Unit  |
| :--------------------------- | :--------------------- | :---- |
| vm.limit.cpu.alerts.count    | Alerts on CPU limit    | Count |
| vm.limit.memory.alerts.count | Alerts on Memory limit | Count |
| vm.limit.disk.alerts.count   | Alerts on Disk limit   | Count |

</TabItem>
</Tabs>

## Prérequis

### Configuration du connecteur Centreon VMWare

Pour la supervision VMWare, centreon utlise un daemon pour se connecter et requêter le vCenter.

Installer le daemon sur tous les pollers :

```
yum install centreon-plugin-Virtualization-VMWare-daemon
```

Pour configurer les accès à votre infrastructure, éditer le fichier
"/etc/centreon/centreon\_vmware.pm" :

``` perl
%centreon_vmware_config = (
    vsphere_server => {
        default => {
            url => 'https://<ip_hostname>/sdk',
            username => '<username>',
            password => '<password>'
        }
    }
);

1;
```

Assurez vous d'avoir remplacé toutes les variables avec les informations nécessaires :

- _ip\_hostname_: Adresse IP ou nom d'hôte du vCenter ou de l'ESX (Si il est en mode standalone),
- _username_: utilisateur avec un accès "lecture seul" au vCenter ou à l'ESX (Vous pouvez utilisez un utilisateur du domaine),
- _password_: le mot de passe de l'utilisateur.

Vous pouvez configurer plusieurs connexions à différents vCenter ou ESX
en utilisant cette structure:

``` perl
%centreon_vmware_config = (
    vsphere_server => {
        'my_first_vcenter' => {
            url => 'https://<ip_hostname>/sdk',
            username => '<username>',
            password => '<password>'
        },
        'my_other_vcenter' => {
            url => 'https://<ip_hostname>/sdk',
            username => '<DOMAIN>\<username>',
            password => '<password>'
        },
    },
    port => 5700
);

1;
```

Chaque entrée est un **container**.

Pour démarrer le daemon et l'activer au démarrage :

``` bash
systemctl start centreon_vmware
systemctl enable centreon_vmware
```

Vous pouvez vérifiez que votre configuration est fonctionelle en consultant les journaux dans :
"/var/log/centreon/centreon\_vmware.log".

### Balises et Attributs personnalisés

Pour découvrir les balises et les attributs personnalisés, vous devez : 

* utiliser la version **3.2.5** de **centreon-vmware-daemon**
* ajouter **--tags** dans les options supplémentaires de découverte : allez à la page **Configuration > Hôtes > Découverte**, et à la 3ème étape (**Définir les paramètres de découverte**), dans la section **Paramètres supplémentaires**, dans le champ **Options supplémentaires**, saisissez **--tags**.

### Flux réseau

Le Collecteur Centreon avec le connecteur VMWare d'installé doit accéder en HTTPS (TCP/443) au vCenter.

Les Collecteurs requêtant le Collecteur avec le connecteur VMWare doit accéder en TCP/5700 au Collecteur avec le Connecteur VMWare.

## Installation

<Tabs groupId="sync">
<TabItem value="Online License" label="Online License">

1. Installer le Plugin sur l'ensemble des Collecteurs Centreon supervisant l'infrastructure VMWare:

```bash
yum install centreon-plugin-Virtualization-Vmware2-Connector-Plugin
```

2. Installer le connecteur de supervision 'Vmware VM' depuis la page **Configuration > Gestionnaire de connecteurs de supervision** sur l'interface Web de Centreon.

</TabItem>
<TabItem value="Offline License" label="Offline License">

1. Installer le Plugin sur l'ensemble des collecteurs Centreon supervisant l'infrastructure VMWare:

```bash
yum install centreon-plugin-Virtualization-Vmware2-Connector-Plugin
```

2. Installer le RPM du connecteur de supervision contenant les modèles de supervision:

```bash
yum install centreon-pack-virtualization-vmware2-vm
```

3. Installer le connecteur de supervision 'Vmware VM' depuis la page **Configuration > Gestionnaire de connecteurs de supervision** sur l'interface Web de Centreon.

</TabItem>
</Tabs>

## Configuration

* Depuis l'interface Web de Centreon, ajoutez un nouvel Hôte depuis la page "Configuration > Hôtes".
* Appliquez le modèle "Virt-VMWare2-VM-custom" et configurez toutes les macros :

| Mandatory   | Name                       | Description                                            |
| :---------- | :------------------------- | :----------------------------------------------------- |
| X           | CENTREONVMWARECONTAINER    | Name of your container in the file centreon_vmware.pm  |
| X           | CENTREONVMWAREHOST         | The Centreon server that launches the connection       |
| X           | CENTREONVMWAREPORT         | By default: 5700                                       |
| X           | VMNAME                     | Name of the VM (defined in your VMWare infrastructure) |
|             | VMUUID                     | Specify The VM UUID                                    |
|             | VMWAREEXTRAOPTIONS         | Customize it with your own if needed                   |

## FAQ

### Comment puis-je tester le Plugin et que signifient les options des commandes ?

Une fois le Plugin installé, vous pouvez tester celui-ci directement en ligne de commande depuis votre collecteur Centreon avec l'utilisateur centreon-engine :

```bash
/usr/lib/centreon/plugins//centreon_vmware_connector_client.pl
	--plugin=apps::vmware::connector::plugin
	--mode=tools-vm
	--custommode=connector
	--connector-hostname='localhost'
	--connector-port='5700'
	--container='vcenter01'
	--vm-hostname='SRV-LIN-TLS'
	--filter-uuid=''
	--tools-notinstalled-status='critical'
	--tools-notrunning-status='critical'
	--tools-notup2date-status='warning'
```

La commande retourne le message de sortie ci-dessous:

```bash
OK: VMTools are OK
```

Cette commande supervise le statut des VMTools (```--plugin=apps::vmware::connector::plugin --mode=tools-vm```) de la VM **SRV-LIN-TLS** (```--vm-hostname='SRV-LIN-TLS'```).
Le plugin se connecte au daemon VMWare sur **localhost** (```--connector-hostname='localhost'```) sur le port **5700** (```--connector-port='5700'```).
Puis la command requête le **container** **vcenter01** (```--container='vcenter01'```) parce que la VM **SRV-LIN-TLS** est managée par **vcenter01**.

La commande déclenchera :
* Une alarme WARNING si les VMTools ne sont pas à jour (```--tools-notup2date-status='warning'```)
* Une alarme CRITICAL si les VMTools ne sont pas démarrés ou installés (```--tools-notinstalled-status='critical' 	--tools-notrunning-status='critical'```)

Vous pouvez afficher tous les modes disponibles à l'aide de la commande suivante :`

```bash
/usr/lib/centreon/plugins/./centreon_vmware_connector_client.pl \
    --plugin=apps::vmware::connector::plugin \
    --list-mode
```

Pour chaque mode, les options disponibles peuvent être consultées en ajoutant l'option --help à la commande :

```bash
/usr/lib/centreon/plugins/./centreon_vmware_connector_client.pl \
    --plugin=apps::vmware::connector::plugin \
    --mode=tools-vm  \
    --help
```

### J'obtiens les erreurs suivantes :

#### UNKNOWN: Unknown container name 'default' |

Ce message d'erreur signifie que le **container* passé en argument n'existe pas dans la configuration du connecteur VMWare.
Vérifiez la macro **CENTREONVMWARECONTAINER** sur l'hôte ou vérifiez la configuration dans le fichier */etc/centreon/centreon_vmware.pm*

#### UNKNOWN: Cannot get response (timeout received)

Ce messsage d'erreur signifie que le plugin n'a pas eu de réponse du daemon VMWare.
Vérifiez vos paramètres de connexion et les macros **CENTREONVMWAREHOST** et **CENTREONVMWAREPORT**.

#### UNKNOWN: Container connection problem |

Ce message signifie que vous avez un problème avec les identifiants liés à votre **container**.
Vérifiez les identifiants dans le fichier */etc/centreon/centreon_vmware.pm*.
Vous pouvez aussi regarder les logs pour plus d'informations: */var/log/centreon/centreon_vmware.log*

#### UNKNOWN: Cannot find 'VirtualMachine' object |

Ce message d'erreur signifie que le plugin n'a pas trouvé la machine virtuelle.
Vérifiez le nom de la machine virtuelle dans la macro **HOSTVMNAME**,
This error message means that the Plugin didn't find the Virtual Machine.
Check the Virtual Machine name in the macro **HOSTVMNAME**, il doit correspondre au nom défini dans votre infrastructure VMWare.
