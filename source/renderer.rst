Écrire un renderer
==================

De nombreux moteurs de rendu ont déja été interfacés avec pyramid,
cependant on peut avoir besoin d'écrire son moteur ou avoir besoin
d'interfacer un nouveau.

Les exemples seront illustrés par pyramid_xslt_ .

.. _pyramid_xslt: https://github.com/cyplp/pyramid_xslt

Pour ajouter un moteur de rendu, on a besoin d'une `factory`, d'un `renderer`
et de rajouter ce rendu à pyramid.

Factory
-------

La `factory` sert à construire le `renderer` qui effetura le rendu.

La signature de la `factory` est la suivante :

.. code-block:: python

  def fatory(info):
      def renderer(value, system):
          pass

      return renderer

ou la version orientée objet :

.. code-block:: python

  class Factory(object):
      def __init__(self, info):
          pass

      def __call__(self, value, system):
          pass

`renderer` et `__call__` doivent retourner une chaine contenant le rendu.

`info` contient un pyramid.renderers.RendererHelper.

Le renderer
-----------

Le rendu doit implémenter l'interface `IRenderer` de pyramid : juste la
méthode `__call__` avec la signature suivante :

.. code-block:: python

 def __call__(self, value, system):
     pass

La méthode `__call__` doit retourner une chaine avec le résulat du rendu.

`value` contient la donnée retournée par la méthode décorée par le `view_config`.
`system` est un dictionnaire contenant :

 - renderer_info : même RendererHelper que `info`,
 - renderer_name : valeur de `renderer` du décorateur `view_config` ; typiquement le chemin vers le template,
 - context : un object pyramid.traversal.DefaultRootFactory,:
 - req : l'objet request,
 - request : le même objet request,
 - view : la fonction décorée correspondans à la vue.


Exemple Complet
---------------

Définition d'un modeur de rendu qui retourne une chaine en
minuscule ou en majuscule.

.. code-block:: python

  class Factory(object):
      def __init__(self, info):
          pass

      def __call__(self, value, system):
          r = Renderer(system['renderer_name'])
	  return r(value, system)

  @implementer(IRenderer)
  class Renderer(object):
      def __init__(self, action):
          self._action = action

      def __call__(self, value, system):
          if self._action == 'lower':
	      retutn value.lower()

	  if self._action == 'upper':
      	      return value.upper()


Ajout du moteur de rendu à pyramid :

.. code-block:: python

 config.add_renderer('lower', Factory)
 config.add_renderer('upper', Factory)

Utilisation du moteur de rendu :

.. code-block:: python

  @view_config(route_name='some route', renderer='upper')
  def someRoute(request):
      return 'FooBar' # Affichera FOOBAR

  @view_config(route_name='other route', renderer='lower')
  def anotherRoute(request):
      return 'FooBar' # Affichera foobar
