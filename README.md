# docker-mannequin

Put [the Mannequin component theming tool][mannequin] into a [Docker][docker] container so we can use it with the [Lando development environment][lando].

[mannequin]: https://mannequin.io
[docker]: https://docker.com
[lando]: https://devwithlando.io

# How to use

1. Follow the [Quick Start instructions on the Mannequin Drupal component page][mannequin-drupal-quickstart], that is to say...
    1. Run `composer require lastcall/mannequin-drupal`
    2. Create a `.mannequin.php` file in your project root as per the template in that documentation page.
2. Add a snippet like the following to a [landofile][landofile]:

        services:
          mannequin:
            type: compose
            services:
              image: mparker17/mannequin-drupal
              command: '/app/vendor/bin/mannequin start *:80'
              ports:
                - '80'
3. Run `lando rebuild` to rebuild your development environment.

    If you've done everything correctly, you should see a "MANNEQUIN URLS" in the "vitals" that Lando reports when it's done [re-]building.

    If not, you might want to check `lando logs -s mannequin` and/or `lando info -s mannequin` for some more information.

[mannequin-drupal-quickstart]: https://mannequin.io/extensions/drupal#quick-start
[landofile]: https://docs.devwithlando.io/config/lando.html

# How it works

If you look closely, you'll notice the snippet tells Lando to run the instance of Mannequin in your app repo; not the one installed in this container. 

So if we don't use the Mannequin installed in this image, why do we need this image at all? Firstly, the Docker image needs to state which port(s) you're planning to expose in order for those ports to be available downstream, i.e.: to `docker compose` and Lando - and the official Docker composer image doen't expose any ports, because it uses the PHP CLI only. Also, it turns out that Lando custom services don't provide a way to set the `WORKDIR` meaning that Mannequin can't find `.mannequin.php` at the root of your site either.

Okay, so we need this container; but why don't we run the instance of Mannequin inside this container? It turns out that Mannequin has a hidden dependency on an instance of Drupal core to run against - so you try to run the mannequin script in this container, it will fail. Why is the dependency on Drupal core hidden? Because neither the Mannequin maintainers nor myself can make any assumptions about which version of Drupal you want to run. Or, more precisely, we could make assumptions, but if we did, then the Mannequin maintainers would have to make a variant of Mannequin for every version of Drupal that exists; and I would have to make an instance of this project for every combination of Drupal version and Mannequin version. This would be time-consuming and quickly get out of hand.
