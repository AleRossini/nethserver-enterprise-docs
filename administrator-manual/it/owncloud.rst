========
ownCloud
========

`ownCloud <http://owncloud.org/>`_ è una soluzione flessibile per la sincronizzazione dei file e la loro condivisione. È possibile avere i propri file sempre a portata di mano su ogni dispositivo, utilizzando un dispositivo mobile, un personal computer, una workstation od un accesso web. La condivisione viene realizzata in maniera semplice, sicura, e privata che significa avere il pieno controllo dei propri dati.

La piattaforma offre inoltre la possibilità di visualizzare, modificare e sincronizzare i propri contatti e calendari su tutti i propri dispositivi.

**Funzionalità:**

* configurazione automatica di :index:`ownCloud` con database mysql
* configurazione di un accesso con credenziali di default
* configurazione automatica di httpd
* integrazione automatica con gli utenti e gruppi di sistema di |product|
* documentazione
* backup dei dati tramite nethserver-backup-data


Installazione
=============

È possibile installare ownCloud tramite l'interfaccia web di |product|.
Dopo l'installazione:

* accedere all'interfaccia web di ownCloud tramite l'url https://your_nethserver_ip/owncloud
* usare le credenziali di default **admin/Nethesis,1234**
* cambiare la password di default

L'autenticazione LDAP è abilitata di default cosicchè ciascun utente presente nel sistema può accedere tramite le sue credenziali.
Dopo l'installazione sarà presente anche un nuovo widget applicativo nella dashboard di |product|.

Configurazione LDAP (solo aggiornamenti da versione 5)
======================================================

.. note:: Questa sezione è da seguire SOLO se è stato eseguito un aggiornamento dalla versione 5 alla versione 7 e non è stata ancora configurata la parte LDAP. Nelle nuove installazioni la configurazione LDAP è eseguita automaticamente. 

#. Copiare la password LDAP con il seguente comando: ::

    cat /var/lib/nethserver/secrets/owncloud

#. Eseguire il login su ownCloud usando l'account amministratore
#. Cercare l'applicazione LDAP user and group backend: *Applicazioni -> LDAP user and group backend*
#. Abilitare "LDAP user and group backend"
#. Configurare i parametri del server: *Admin -> Admin -> tab Server*
#. Completare il tab "Server" con i seguenti parametri: ::

    Host: localhost:389
    Porta: 389
    DN utente: cn=owncloud,dc=directory,dc=nh
    Password: "you can use copied password"
    DN base: dc=directory,dc=nh

#. Completare il tab "Filtro utente" con: ::

    Modifica invece il filtro grezzo: (&(objectClass=person)(givenName=*))

#. Completare il tab "Filtro accesso" con: ::

    Modifica invece il filtro grezzo: uid=%uid

#. Completare il tab "Filtro gruppo" con: ::

    Modifica invece il filtro grezzo: (&(objectClass=posixGroup)(memberUid=*))

#. Configurare il tab "Avanzate" con: ::

    Impostazioni delle cartelle
        Campo per la visualizzazione del nome utente: cn
        Struttura base dell'utente: dc=directory,dc=nh
        Campo per la visualizzazione del nome del gruppo: cn
        Struttura base del gruppo: dc=directory,dc=nh
        Associazione gruppo-utente -> memberUid

    Attributi speciali
        Campo Email: email

#. Configurare il tab "Esperto" con: ::

    Attributo nome utente interno: uid
    Clicca "Cancella associazione Nome utente-Utente LDAP" 

#. Clicca il pulsante "Salva"

Note su LDAP
============


Lista utenti
------------

Dopo aver configurato ownCloud con LDAP, la lista utenti potrebbe mostrare qualche nome contenente dei numeri casuali.
È una soluzione adottata da ownCloud per garantire che non ci siano nomi duplicati. Per maggiori informazioni leggere `Internal Username. <http://doc.owncloud.org/server/6.0/admin_manual/configuration/auth_ldap.html#expert-settings>`_

Se la lista utenti contiene due amministratori, questi sono di ownCloud e LDAP. È quindi possibile rimuovere quello di ownCloud dopo aver assegnato l'utente amministratore di LDAP al gruppo amministratore. In questo modo è possibile usare solo quello di LDAP. Per farlo è sufficiente procedere nel seguente modo:

#. eseguire il login a ownCloud come amministratore
#. aprire la lista utenti: *admin -> Utenti*
#. cambiare il gruppo dell'utente "admin_xxx", selezionando "admin"
#. cambiare la password dell'utente admin di ownCloud
#. eseguire il logout e login tramite l'utente admin di LDAP
#. rimuovere l'utente admin di ownCloud (chiamato "admin")


Trusted Domains
===============

I `Trusted domains <https://doc.owncloud.org/server/7.0/admin_manual/configuration/config_sample_php_parameters.html>`_ sono una lista di domini su cui l'utente può effettuare il login. Quelli presenti di default sono:

* nome dominio
* indirizzo ip

Per aggiungere uno nuovo eseguire: ::

    config setprop owncloud TrustedDomains server.domain.com
    signal-event nethserver-owncloud-update

Per aggiungere più di uno è sufficiente concatenare i nomi con una virgola.
