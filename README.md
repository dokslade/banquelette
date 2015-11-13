# Banquelette 

## Description 
Banquelette est un outil pour gérer ces comptes.

## Lancement via Docker
Banquelette est packagée pour Docker. Vous pouvez directement télécharger une
image Docker fonctionnelle ou construire vous-même votre image (si vous
souhaitez être certain d'avoir les tous derniers commits, par exemple).

Vous aurez également besoin d'une image Docker de CouchDB pour héberger la base
de donnée ([celle de klaemo](https://hub.docker.com/r/klaemo/couchdb)
fonctionne par exemple très bien).

Deux variables d'environnement principales sont à définir au moment de lancer
l'image:

- ***COUCHDB_HOST*** contient l'adresse de la base de donnée (au format
  hostname:port). Par défaut, elle vaut *db:5984*
- ***SECRET_KEY*** est la clef secrète Django. Elle vaut par défaut une valeur
  arbitraire. **Il est _TRÈS_ important de la changer**

### Image docker préconstruite

L'image à télécharger sur le Docker Hub est
[lertsenem/banquelette](https://hub.docker.com/r/lertsenem/banquelette). Je
suppose que vous disposez déjà de l'image *klaemo/couchdb* pour héberger la
base de donées CouchDB. Remplacez-la éventuellement par ce que vous voulez.

```
	docker pull lertsenem/banquelette

	docker run --name couchdb_banquelette klaemo/couchdb

	docker run \
		--name banquelette \
		--link couchdb_banquelette:couchdb \
		-e "COUCHDB_HOST=couchdb:5984" \
		-e "SECRET_KEY=12345678abcdef" \
		lertsenem/banquelette
```


### Construire votre image Docker

Tous les fichiers nécessaires sont déjà présents dans le repo git, qu'il va
vous falloir cloner. Encore une fois ici, j'utilise l'image docker CouchDB de
klaemo. Remplacez-la par ce que vous souhaitez.

```
	git clone https://github.com/mellecrepe/banquelette.git

	cd banquelette

	docker build -t test/banquelette .

	docker run --name couchdb_banquelette klaemo/couchdb

	docker run \
		--name banquelette \
		--link couchdb_banquelette:couchdb \
		-e "COUCHDB_HOST=couchdb:5984" \
		-e "SECRET_KEY=12345678abcdef" \
		test/banquelette
```

## Lancement manuel
### Installation 
Paquets à installer : *python2.7*, *couchdb*, *django1.8*, *couchdbkit*
Par exemple, sous debian/ubuntu :
```
	apt-get install couchdb python-django 
        pip install couchdbkit
```

Assurez-vous d'avoir la version 1.8 grâce à cette commande :
```
	django-admin --version
```

Si la version de django est inférieur à 1.8, upgradez via cette commande :
```
	pip install django --upgrade
```

Couchdbkit est une bibliotheque qui n'est plus maintenue et qui utilise une
bibliotheque obsolete de django.
Pour corriger ça, editer le fichier suivant :
```
	vim /usr/local/lib/python2.7/dist-packages/couchdbkit/ext/django/schema.py
```

Puis commenter les lignes **24** et **64** (références à la fonction *get_verbose_name*)
```
	#from django.db.models.options import get_verbose_name
```


### Configuration
#### configuration couchdb
Créer une database nommée : **account**

Afin que l'application fontionne un certain nombre de views doivent être créées
dans cette database. Le plus simple pour ce faire et d'utiliser le script
*initdb.sh* (une aide sucinte est incluse).

#### configuration de l'application banquelette
Il existe trois fichiers de configuration :

- *projet/settings.py*  définit les paramètres génériques, notamment la clef
  secrète Django et la connexion à CouchDB ;
- *account/settings.py* definit les parametres liés aux banques ;
- *account/data.py* permet d'automatiser la modification de certains
  descriptifs ou de definir à quel categorie appartient la depense.
 
