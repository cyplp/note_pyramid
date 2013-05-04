Système de routes
=================

`pyramid` utilise un système de route prédéclarées dans le fichier `__init__.py`.

.. Note:: En réalité, `pyramid` offre d'autres possiblités détaillées dans leur documentation.
          Je n'aborderais pas ces alternatives.

Les routes sont déclarées par un couple : le nom de la route et un motif.
ex :

.. code-block:: python

   config.add_route('home', '/')

Ici `home` est le  nom de la route et `/` est le motif correspondant à la racine du site.

Les motifs décrivent les chemins du site web.
ex:

 - /
 - /foo
 - /foo/bar

Ces motifs décrivent des chemins prédéfinis. Ces motifs peuvent être variables pour décrire des chemins variables :

 - /user/{id}
 - /user/{id}/contacts
 - /user/{id}/contacts/{contact}
 - /foo/{bar}/{baz}

ces motifs peuvent faire matcher les chemins suivants :

 - /user/1
 - /user/2
 - /user/a
 - /user/a/contacts
 - [...]

Les parties entre accolades sont contenus dans des variables donc l'accès sera décrit plus tard.
Les motifs peuvent également contenir des expressions rationnelles :

 - /user/{id:\d*}
 - /user/{id: \w{4}}

Les urls ne seront matchées que si elles correspondent aux motifs. Ces expressions rationnelles permettent
également d'avoir des routes différentes pour des url similaires:


.. code-block:: python

   config.add_route('userDigit', '/user/{id: \d*}')
   config.add_route('userAlpha', '/user/{id: \w*}')

La première route correspondera à `/user/1232445` et la seconde à `/user/azerty`. Les 2 routes pourront être gérées
différement les views.


