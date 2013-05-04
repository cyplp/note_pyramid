Structure de l'application
==========================

Le template créé la structure de l'egg et un certain nombre de fichiers :
 - setup.py,
 - un fichier de configuration de dev,
 - un fichier de configuration de production,
 - un répertoire avec un semble de fichiers.

Ce répertoire contient 3 fichier et 2 répertoires:
 - __init__.py,
 - tests.py,
 - views.py,
 - static,
 - templates.

__init__.py
-----------

`__init__.py` est le fichier « racine » de l'application. Il contient l'appel à la configuration et
la déclaration de singleton, connexion à des bases de données etc.

.. code-block:: python
  :linenos:
  :emphasize-lines: 7, 9, 11, 12

  from pyramid.config import Configurator

  def main(global_config, **settings):
      """
      This function returns a Pyramid WSGI application.
      """
      config = Configurator(settings=settings)

      config.add_static_view('static', 'static', cache_max_age=3600)

      config.add_route('home', '/')
      config.scan()
      return config.make_wsgi_app()


Ligne 7, on configure `pyramid` : `settings` est un dictionnaire qui contient la configuration de présente
dans le fichier de configuarion.

Ligne 9, on déclare le dossier contenant les fichiers statiques : css, js favicon...

Ligne 11, une route nommée `home` est déclarée sur `/`.

Ligne 13, tous les fichiers sont scannées pour trouver les vues, routes et autres « pyramideries ».

tests.py
--------

Ce fichier contient les tests unitaires de l'application. Pour le moment, je n'aborderais pas ce point.

views.py
--------

Le fichier contient le code des vues et les appels aux templates.


.. code-block:: python
  :linenos:
  :emphasize-lines: 3, 5

  from pyramid.view import view_config

  @view_config(route_name='home', renderer='templates/home.pt')
  def home(request):
      return {'project':'cyplp.timelapse_commander'}


Ligne 3, la fonction décorée à la ligne 4 corresponds à la route `home` et utilise le rendu `home.pt`
présent dans le dossier `template`.

Ligne 5, la fonction retourne les paramêtres du template.

static
------

Comme dit plus haut, ce dossier contient les fichiers statiques.

templates
---------

Ce dossier contient les templates.
