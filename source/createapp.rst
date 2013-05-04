
Créer une application pyramid
=============================

Installation des prérequis
--------------------------

Extrait du fichier buildout:

.. code-block:: ini

  [buildout]
  parts =
         myapp

  [myapp]
  recipe = zc.recipe.egg
  eggs =
         pastescript
	 pyramid

Création de l'egg
-----------------

Une fois la part installée, `pcreate` est dispo dans `bin`. `pcreate` permet de
générer l'egg de l'application. `pcreate` vient avec 3 templates pour `pyramid`.

.. code-block:: bash

   $ bin/pcreate --list-templates
   Available scaffolds:
   alchemy:            Pyramid SQLAlchemy project using url dispatch
   starter:            Pyramid starter project
   zodb:               Pyramid ZODB project using traversal

Pour créer une application: `bin/pcreate -s <votre template> <nom de l'egg>`.

Il existe d'autre templates disponible sur le pypi_.

.. _pypi: http://pypi.python.org/pypi
