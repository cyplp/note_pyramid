Les views
=========

le fichier `views.py` rassemble les différentes vues :

.. code-block:: python

  from pyramid.view import view_config

  @view_config(route_name='home', renderer='templates/home.pt')
  def home(request):
      return {'project':'cyplp.timelapse_commander'}

  @view_config(route_name='controls', renderer='templates/controls.pt')
  def controls(request):
      return {'status': 'ok',
              'values': [1, 2, 3]}

  # [SNIP]


Les vues peuvent être éclatés dans plusieurs fichiers, peuvent être écrites sous forme de classes.

.. code-block:: python

   class ViewSample(object):
   	def __init__(self, request):
	    self._request = request

	@view_config(route_name='home', renderer='templates/home.pt')
        def home(self):
            return {'project':'cyplp.timelapse_commander'}

        @view_config(route_name='controls', renderer='templates/controls.pt')
	def controls(request):
            return {'status': 'ok', 'values': [1, 2, 3]}


Une vue est une fonction décorée par `view_config`. La fonction prends en paramètre un objet `request` (abordé plus tard)
qui corresponds à la requête faite par l'utilisateur. La fonction retourne ou un objet `Response` (abordé plus tard)
ou objet manipulable par le moteur de rendu. Le décorateur `view_config` décrit le rendu de la vue et son appel.

`view_config` peut prendre un grand nombre de paramètres :
 - route_name,
 - renderer,
 - request_method,
 - accept,
 - permission,
 - context,
 - name,
 - request_type,
 - request_param,
 - containment,
 - xhr,
 - header,
 - path_info,
 - custom_predicates,
 - decorator,
 - mapper,
 - http_cache,
 - match_param,
 - csrf_token,
 - physical_path,
 - predicates.

Tous sont optionnels. Seuls les cinq premiers seront décrits ici.

route_name
----------

`route_name` décrit quelle route est rendu par cette fonction. `route_name` corresponds au nom de la route rajouté dans le `config.add_route`
du `__init__.py`. Dans certaines conditions, plusieurs fonctions peuvent avoir le même `route_name` (voir plus bas.)

renderer
--------

`renderer` décrit le rendu utilisé lors de l'appel. Par défaut, `pyramid` est livré avec 2 moteurs de rendu : `json` et `chameleon`.
D'autres moteurs sont disponibles tel que `mako`, `genshi`, `jinja` ou même un moteur maison. Je ne parlerais que de `json` et `chameleon`.

Le renderer `json` transforme la réponse en json ; la réponse doit donc être une chaine de caractères, une liste, un tuple,
un dictionnaire ou une combinaison des précédents.

.. Note:: Renvoyer une liste en json est potentiellement une faille de sécurité. `pyramid` affiche un warnings dans ce cas qui
          contient un lien vers l'explication. Renvoyer un dictionnaire : {"data": [item1, item2]} est préférable.


`renderer` peut aussi prendre un chemin vers un template tel que `templates/foo.pt`. l'extention .pt signifie page template et
indique que le moteur de rendu e ici est chameleon_. Les rendus chameleon_ prennent en parametre des dictionnaires pouvant contenir
des objets.

.. _chameleon: http://chameleon.readthedocs.org


request_method
--------------

`request_method` est le verbe HTTP utilisé pour acceder à la page : `GET`, `POST`...

accept
------

`accept` liste les types de réponses acceptées par le client : `application/json`, `application/xml`, `text/html`...


Combinaison des paramêtres
---------------------------

tous ces paramètres peuvent se combiner pour offrir une granularité fine pour les réponses.

.. code-block:: python

  @view_config(name="home", accept='application/json', renderer='json')
  def repJson(request):
      """réponse en json"""
      return {'foo': 'bar'}

  @view_config(name="home", request_method='GET', renderer='templates/home.pt')
  def repGet(request):
      return {'params': 'foo'}

  @view_config(name="home", request_method='POST', renderer='templates/home.pt')
  def repPost(request):
      return {'params': 'bar'}

  @view_config(name="home", request_method='PUT', renderer='templates/homeput.pt')
  def repPut(request):
      return {'params': 'baz'}

Dans cet exemple, la même route `home` peut répondre selon les combinaisons de 4 manières différentes en utilisant s'il le
faut différents rendus.


Ajout de paramêtres
-------------------

Si la liste ne suffit pas, il est possible de créer ses propres prédicats.

Il n'y a pas d'interface au sens zope mais la classe doit respecter la signature
suivante :

.. code-block:: python

 class Predicate(object):
    def __init__(self, val, config):
        pass

    def text(self):
        return 'une signature'

    phash = text

    def __call__(self, context, request):
        return True # retourne un booléen

Exemple de prédicat sur le jour de la semaine.

.. code-block:: python

 class DayPredicate(object):
       _choice = {'monday': 0,
                  'thursday': 1,
		  'wedesnday': 2,
		  'thuesday' : 4,
		  'friday': 5,
		  'saturday': 6,
		  'sunday': 7}

       def __init__(self, val, config):
	   self._val = val
	   self._numVal = self._choice[val]

       def text(self):
           return 'predicat on %s' % self._val

       phash = text

       def __call__(self, context, request):
           return datetime.datetime.today().weekday() == self._numVal

Ajout du prédicat à pyramid:

.. code-block:: python

 config.add_view_predicate('day', DayPredicate)

et enfin l'utilisation.

.. code-block:: python

 @view_config(route_name='some route', renderer='template/monday.pt', day='monday')
 def monday(request):
     pass

 @view_config(route_name='some route', renderer='template/sunday.pt', day='sunday')
 def sunday(request):
     pass

Une autre approche est d'utiliser le parametre custom_predicate de `view_config`.

.. code-block:: python

 @view_config(route_name='some route', renderer='template/monday.pt', custom_predicate=(DayPredicate('monday', None), ))
 def monday(request):
     pass

 @view_config(route_name='some route', renderer='template/sunday.pt', custom_predicate=(DayPredicate('sunday', None), ))
 def sunday(request):
     pass
