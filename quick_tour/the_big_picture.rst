Základní přehled
===============

Začněte používat Symfony2 v 10 minutách! Tato kapitola vás provede některými 
nejdůležitějšími koncepcemi v Symfony2 a ukáže vám, jak rychle začít na příkladu 
jednoduchého projektu v akci.  

Pokud jste již v minulosti používali nějaký webový framework, měly byste se
v Symfony2 cítit jako doma. V opačném případě vítejte ve zcela novém způsobu
vývoji webových aplikací.


Instalace Symfony2
-------------------

Nejprve si ověřte, že verze PHP nainstalovaná ve vašem počítači splňuje požadavky
Symfony2: verze PHP 5.3.3 nebo vyšší. Po té otevřte konsoli a spusťte následující
příkaz v adresáři ``mujprojekt/``, který nainstaluje nejnovější verzi Symfony2:

.. code-block:: bash

    $ composer create-project symfony/framework-standard-edition mujprojekt/ ~2.4

.. note::

    `Composer`_ je manažer balíčků používaný moderními PHP aplikacemi a je to
    jediný doporučený způsob instalace Symfony2. Pro instalaci Composeru na 
    vašem systému Linux nebo Mac, spusťe následující příkazy:

    .. code-block:: bash

        $ curl -sS https://getcomposer.org/installer | php
        $ sudo mv composer.phar /usr/local/bin/composer

    Pro instalaci Composeru na sytstému Windows si stáhněte `executable installer`_.

Pozor, první instalace Symfony2 může trvat několik minut, než se stáhnout všechny 
její komponenty. Na konci instalačního procesu se vás instalátor zeptá na různé
konfigurační možnosti projektu Symfony2. Během instalace prvního projektu
můžete tuto konfiguraci klidně ignorovat opakovaným stisknutím tlačitka ``<Enter>``.

Spuštění Symfony2
----------------

Před prvním spuštěním Symfony2, spusťe následující příkaz, aby jste se ujistili, 
že váš systém splňuje všechny technické požadavky:

.. code-block:: bash

    $ cd mujproject/
    $ php app/check.php

Opravte všechny chyby reportované příkazem a pak použijte zabudovaný PHP web server
ke spuštění Symfony:

.. code-block:: bash

    $ php app/console server:run

Pokud se zobrazí chyba `There are no commands defined in the "server" namespace.`,
používáte pravděpodobně PHP ve verzi 5.3. To je v pořádku! Jen zabudovaný web server
je určen pouze pro verzi PHP 5.4.0 nebo vyšší. Pokud máte starší verzi PHP nebo preferujete
tradiční web servery jako jsou Apache nebo Nginx, přečtěte si článek
:doc:`/cookbook/configuration/web_server_configuration`.


Otevřete váš prohlížeč a přejděte na URL adresu ``http://localhost:8000``. Měly byste vidět
uvítací stránku (Welcome page) Symfony2:

.. image:: /images/quick_tour/welcome.png
   :align: center
   :alt:   Symfony2 Welcome Page

Porozumnění základům
------------------------------

Jedním z hlavních cílů frameworku je udžet váš kód organizovaný, tak aby umožňoval snadný 
vývoj aplikace v čase, tím že se vyhnete míchání databázových volání, HTML tagů
a obchodní logiky v jednom skriptu. K dosažení tohoto cíle se musíte v Symfony 
nejdříve naučit několika základním konceptům a termínům.

Symfony přináší několik ukázkových kódů, díky kterým se můžete naučit
více o jeho hlavních konceptech. Přejděte na následující URL adresu, aby jste se
seznámili se Symfony2 (nahraďte *Fabien* vaším křestním jménem):

.. code-block:: text

    http://localhost:8000/demo/hello/Fabien

.. image:: /images/quick_tour/hello_fabien.png
   :align: center

Co se zde děje? Podívejme se na jednotlivé částí URL adresy:

* ``app_dev.php``: Toto je :term:`front controller`. To je jedinečný vstupní bod aplikace,
  který odpovídá na všechny uživatelské požadavky;

* ``/demo/hello/Fabien``: Toto je *virtuální cesta* ke zdroji, který chce uživatel zpřístuonit.

Vaše zodpovědnost jako programátora je napsat kód, který mapuje uživatelský
*požadavek* (``/demo/hello/Fabien``) na *zdroj* s tímto požadavkem spojený
(v tomto případě to je HTML stránka ``Hello Fabien!``).

Směrování
~~~~~~~

Symfony2 směřuje požadavky do kódu, který zpracovává požadovanou URL adresu 
(například virtuální cestu), jenž odpovídá nějaké nakonfiguravné cestě. V našem demu 
jsou cesty definovány v konfuguračním souboru ``app/config/routing_dev.yml``:

.. code-block:: yaml

    # app/config/routing_dev.yml
    # ...

    # AcmeDemoBundle routes (to be removed)
    _acme_demo:
        resource: "@AcmeDemoBundle/Resources/config/routing.yml"

Tímto se importuje soubor ``routing.yml``, který se nachází u vnitř AcmeDemoBundle:

.. code-block:: yaml

    # src/Acme/DemoBundle/Resources/config/routing.yml
    _welcome:
        path:     /
        defaults: { _controller: AcmeDemoBundle:Welcome:index }

    _demo:
        resource: "@AcmeDemoBundle/Controller/DemoController.php"
        type:     annotation
        prefix:   /demo

    # ...

První tři řádky (za komentářem) určují kód, který se spustí, když uživatel
požaduje zdroj "``/``" (například uvítací stránku, kterou jsme viděli výše). 
Při zadání tohoto požadavku se spustí řadič ``AcmeDemoBundle:Welcome:index``.
V následující sekci se naučíte, co přesně to znamená.

.. tip::

    Kromě souborů YAML, může být směrování konfigurováno v XML nebo PHP souborech
    a dokonce může být vloženo do PHP anotace, Tato flexibilita je jedním z hlavních
    znaků Symfony2, frameworku, který vám nikdy neurčuje konkrétní konfigurační
    formát.

Řadiče
~~~~~~~~~~~

Řadič (controller) je funkce nebo metoda PHP, která zpracovává příchozí *požadavky*
a vrací *odpovědi* (často HTML kód). Místo používání globálních proměných 
a funkcí (jako jsou ``$_GET`` nebo ``header()``), používá Symfony k řízení 
těchto HTTP zpráv objekty: :ref:`Request <component-http-foundation-request>`
and :ref:`Response <component-http-foundation-response>`. The simplest possible
controller might create the response by hand, based on the request::

    use Symfony\Component\HttpFoundation\Response;

    $name = $request->get('name');

    return new Response('Hello '.$name);

Symfony2 chooses the controller based on the ``_controller`` value from the
routing configuration: ``AcmeDemoBundle:Welcome:index``. This string is the
controller *logical name*, and it references the ``indexAction`` method from
the ``Acme\DemoBundle\Controller\WelcomeController`` class::

    // src/Acme/DemoBundle/Controller/WelcomeController.php
    namespace Acme\DemoBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class WelcomeController extends Controller
    {
        public function indexAction()
        {
            return $this->render('AcmeDemoBundle:Welcome:index.html.twig');
        }
    }

.. tip::

    You could have used the full class and method name -
    ``Acme\DemoBundle\Controller\WelcomeController::indexAction`` - for the
    ``_controller`` value. But using the logical name is shorter and allows
    for more flexibility.

The ``WelcomeController`` class extends the built-in ``Controller`` class,
which provides useful shortcut methods, like the
:ref:`render()<controller-rendering-templates>` method that loads and renders
a template (``AcmeDemoBundle:Welcome:index.html.twig``). The returned value
is a ``Response`` object populated with the rendered content. So, if the need
arises, the ``Response`` can be tweaked before it is sent to the browser::

    public function indexAction()
    {
        $response = $this->render('AcmeDemoBundle:Welcome:index.txt.twig');
        $response->headers->set('Content-Type', 'text/plain');

        return $response;
    }

No matter how you do it, the end goal of your controller is always to return
the ``Response`` object that should be delivered back to the user. This ``Response``
object can be populated with HTML code, represent a client redirect, or even
return the contents of a JPG image with a ``Content-Type`` header of ``image/jpg``.

The template name, ``AcmeDemoBundle:Welcome:index.html.twig``, is the template
*logical name* and it references the ``Resources/views/Welcome/index.html.twig``
file inside the AcmeDemoBundle (located at ``src/Acme/DemoBundle``).
The `Bundles`_ section below will explain why this is useful.

Now, take a look at the routing configuration again and find the ``_demo``
key:

.. code-block:: yaml

    # src/Acme/DemoBundle/Resources/config/routing.yml
    # ...
    _demo:
        resource: "@AcmeDemoBundle/Controller/DemoController.php"
        type:     annotation
        prefix:   /demo

The *logical name* of the file containing the ``_demo`` routes is
``@AcmeDemoBundle/Controller/DemoController.php`` and refers
to the ``src/Acme/DemoBundle/Controller/DemoController.php`` file. In this
file, routes are defined as annotations on action methods::

    // src/Acme/DemoBundle/Controller/DemoController.php
    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Template;

    class DemoController extends Controller
    {
        /**
         * @Route("/hello/{name}", name="_demo_hello")
         * @Template()
         */
        public function helloAction($name)
        {
            return array('name' => $name);
        }

        // ...
    }

The ``@Route()`` annotation creates a new route matching the ``/hello/{name}``
path to the ``helloAction()`` method. Any string enclosed in curly brackets,
like ``{name}``, is considered a variable that can be directly retrieved as a
method argument with the same name.

If you take a closer look at the controller code, you can see that instead of
rendering a template and returning a ``Response`` object like before, it
just returns an array of parameters. The ``@Template()`` annotation tells
Symfony to render the template for you, passing to it each variable of the
returned array. The name of the template that's rendered follows the name
of the controller. So, in this example, the ``AcmeDemoBundle:Demo:hello.html.twig``
template is rendered (located at ``src/Acme/DemoBundle/Resources/views/Demo/hello.html.twig``).

Šablony
~~~~~~~~~

The controller renders the ``src/Acme/DemoBundle/Resources/views/Demo/hello.html.twig``
template (or ``AcmeDemoBundle:Demo:hello.html.twig`` if you use the logical name):

.. code-block:: jinja

    {# src/Acme/DemoBundle/Resources/views/Demo/hello.html.twig #}
    {% extends "AcmeDemoBundle::layout.html.twig" %}

    {% block title "Hello " ~ name %}

    {% block content %}
        <h1>Hello {{ name }}!</h1>
    {% endblock %}

By default, Symfony2 uses `Twig`_ as its template engine but you can also use
traditional PHP templates if you choose. The
:doc:`second part of this tutorial</quick_tour/the_view>` will introduce how
templates work in Symfony2.

Svazky
~~~~~~~

You might have wondered why the :term:`Bundle` word is used in many names you
have seen so far. All the code you write for your application is organized in
bundles. In Symfony2 speak, a bundle is a structured set of files (PHP files,
stylesheets, JavaScripts, images, ...) that implements a single feature (a
blog, a forum, ...) and which can be easily shared with other developers. As
of now, you have manipulated one bundle, AcmeDemoBundle. You will learn
more about bundles in the :doc:`last part of this tutorial</quick_tour/the_architecture>`.

.. _quick-tour-big-picture-environments:

Working with Environments
-------------------------

Now that you have a better understanding of how Symfony2 works, take a closer
look at the bottom of any Symfony2 rendered page. You should notice a small
bar with the Symfony2 logo. This is the "Web Debug Toolbar", and it is a
Symfony2 developer's best friend!

.. image:: /images/quick_tour/web_debug_toolbar.png
   :align: center

But what you see initially is only the tip of the iceberg; click on any of the
bar sections to open the profiler and get much more detailed information about
the request, the query parameters, security details, and database queries:

.. image:: /images/quick_tour/profiler.png
   :align: center

Of course, it would be unwise to have this tool enabled when you deploy your
application, so by default, the profiler is not enabled in the ``prod``
environment.

.. _quick-tour-big-picture-environments-intro:

What Is an environment?
~~~~~~~~~~~~~~~~~~~~~~~

An :term:`Environment` represents a group of configurations that's used to run
your application. Symfony2 defines two environments by default: ``dev``
(suited for when developing the application locally) and ``prod`` (optimized
for when executing the application on production).

Typically, the environments share a large amount of configuration options. For
that reason, you put your common configuration in ``config.yml`` and override
the specific configuration file for each environment where necessary:

.. code-block:: yaml

    # app/config/config_dev.yml
    imports:
        - { resource: config.yml }

    web_profiler:
        toolbar: true
        intercept_redirects: false

In this example, the ``dev`` environment loads the ``config_dev.yml`` configuration
file, which itself imports the common ``config.yml`` file and then modifies it
by enabling the web debug toolbar.

When you visit the ``app_dev.php`` file in your browser, you're executing
your Symfony application in the ``dev`` environment. To visit your application
in the ``prod`` environment, visit the ``app.php`` file instead.

The demo routes in our application are only available in the ``dev`` environment.
Therefore, if you try to access the ``http://localhost/app.php/demo/hello/Fabien``
URL, you'll get a 404 error.

.. tip::

    If instead of using PHP's built-in webserver, you use Apache with
    ``mod_rewrite`` enabled and take advantage of the ``.htaccess`` file
    Symfony2 provides in ``web/``, you can even omit the ``app.php`` part of the
    URL. The default ``.htaccess`` points all requests to the ``app.php`` front
    controller:

    .. code-block:: text

        http://localhost/demo/hello/Fabien

For more details on environments, see
":ref:`Environments & Front Controllers <page-creation-environments>`" article.

Final Thoughts
--------------

Congratulations! You've had your first taste of Symfony2 code. That wasn't so
hard, was it? There's a lot more to explore, but you should already see how
Symfony2 makes it really easy to implement web sites better and faster. If you
are eager to learn more about Symfony2, dive into the next section:
":doc:`The View<the_view>`".

.. _Composer:             https://getcomposer.org/
.. _executable installer: http://getcomposer.org/download
.. _Twig:                 http://twig.sensiolabs.org/
