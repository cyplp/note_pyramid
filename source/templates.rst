
Les templates
===============

Je ne parlerais ici que de chameleon_ mais il existe bien d'autres moteurs de templates.

.. _chameleon: http://chameleon.readthedocs.org

chameleon_ est un moteur de template xml. Il utilise 4 namespaces :
 - tal : instuctions de bases,
 - tales : instructions d'extention du langage,
 - metal : instructions de macro,
 - i18n : gestion de l'internationnalisation.


exemple :

.. code-block:: xml

 <table>
  <tr tal:repeat="row rows">
    <td>${row.col1}</td>
    </td>${row.foo + row.bar}</td>
  </tr>
  </table>

tal
---

tal comprends la liste d'instructions suivantes :

 - `tal:define`: définit une variable,
 - `tal:switch`: switch (comme en C switch case),
 - `tal:case`: case du switch plus haut,
 - `tal:condition`: instruction if,
 - `tal:repeat`: boucle for,
 - `tal:content`: remplace un contenu par un autre,
 - `tal:replace`: idem mais dynamique,
 - `tal:omit-tag` : supprime les balises filles,
 - `tal:attributes`: ajout ou modifie des attributs,
 - `tal:on-error`: en cas d'erreur.

tal:define
++++++++++

L'instruction permet de définir une variable dans le template :

.. code-block:: xml

 <a tal:define="variable object_passe_au_template.methode_couteuse()">
  <b>${variable.lower()}</b>
  <c>${variable.upper()}</c>
 </a>

tal:switch et tal:case
++++++++++++++++++++++

Ce couple définit le classique couple switch/case (absent de python).

.. code-block:: xml

 <ul tal:switch="getLevel()">
   <li tal:case='debug' class="white">...</li>
   <li tal:case='info' class="green">...</li>
   <li tal:case='warning' class="orange">...</li>
   <li tal:case='fatal' class="red">...</li>
 </ul>

tal:condition
+++++++++++++

L'instruction permet de définir un if.

.. code-block:: xml

 <div>
   <span>Bienvenue <strong tal:condition="username is not None">${username}</strong></span>
 </div>

tal:repeat
++++++++++

L'instruction permet de définir une bouche for.

.. code-block:: xml

 <table>
  <tr tal:repeat="row rows">
    <td>${row.col1()}</td>
    </td>${row.foo + row.bar}</td>
  </tr>
 </table>


tal:content et tal:replace
++++++++++++++++++++++++++

L'instruction `tal:content` positionne un contenu.

.. code-block:: xml

 <c tal:content='someVariable' />

Si la variable contient 'foo' cela donnera :

.. code-block:: xml

 <c>foo</c>

L'instruction `tal:content` est équivalente à:

.. code-block:: xml

 <c>${someVariable}</c>

L'instruction `tal:replace` permet de remplacer un contenu par un autre.

.. code-block:: xml

 <div tal:replace="realContent">Lorem ipsum dolora...</div>

Cela permet de fournir un template complet avec un faux contenu à un infographiste ou un intégrateur.

`tal:content` et `tal:replace` peuvent prendre une instruction pour interpreter le contenu : `string:` et `structure:`

`structure:` permet de ne pas échapper le xml de la variable.

Si a='<a/>'

.. code-block:: xml

 <b tal:content='a'/> <!-- <b>&lt;a/&gt;</b> -->
 <b tal:content='structure :a'/> <!-- <b><a/></b> -->
