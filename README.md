Private GOGS ( github-clone ) served through HTTPS via NGINX
==========================================================

## Requirement
* [Docker](https://docs.docker.com/linux/step_one/) (>= 1.10.3)
* [docker-compose](https://docs.docker.com/compose/install/) (>= 1.6.2)

# Build
After installing the requirements, run inside this directory:

```
docker-compose up
```

## First run
### Self-signed certificates using gogs
During the first run, the `nginx` container `webserver` will complain that no certificates are available and will quit. (But `gitserver` will keep running.)

To solve that, open a terminal and execute (replace `(gitserver)` with the name of the container as printed by `docker-compose up` call):

```
docker exec -it (gitserver) bash
```

This will connect you to the running `gitserver` container and will launch an instance of bash inside it. Then enter (replace again `(hostname)` here with your domain name):

```
cd /data/gogs/conf/
/app/gogs/gogs cert -ca=true -duration=8760h0m0s -host=(hostname)
exit
```

That's it. You now have self-signed certificates by `gogs`. You can now restart the failed container:
```
docker-compose up
```

### Autorithy signed certificates
If you already have signed certificates, just copy them into: `/var/gogs/gogs/conf` in your local system. They will automatically mounted during when `docker-compose` is launched.

Remember to rename the certificate: `cert.pem`, and the server key: `key.pem`.


# Usage

You can now acces you self-hosted `gogs` service through HTTPS using any browser. You'll get a warning however as the certificate are self-signed.

### `git` through self-signed HTTPS
To use `git` with a self-signed, you need to disable SSL verification temporarly:
```
export GIT_SSL_NO_VERIFY=true
```
or permanently (:warning: ***DANGEROUS*** :no_pedestrians:):
```
git config --global http.sslverify false
```

### `git` through ssh on a different port (*10022*)
To use `git` with `ssh` on a different port than the default port *22*:
```
git clone ssh://git@hostname:10022/user/repo.git
```

Remember to add your `ssh` keys first!

# References
* [gogs/docker github](https://github.com/gogits/gogs/tree/master/docker)
* [Unknown blog post](https://unknwon.io/setup-gogs-with-https/)
* [ANAND MANI SANKAR blog post](http://anandmanisankar.com/posts/docker-container-nginx-node-redis-example/)
* [DigitalOcean Tutorial 1](https://www.digitalocean.com/community/tutorials/understanding-the-nginx-configuration-file-structure-and-configuration-contexts)
* [DigitalOcean Tutorial 2](https://www.digitalocean.com/community/tutorials/how-to-run-nginx-in-a-docker-container-on-ubuntu-14-04)
* [stackoverflow question](http://stackoverflow.com/questions/21181231/server-certificate-verification-failed-cafile-etc-ssl-certs-ca-certificates-c)
* [misho gist](https://gist.github.com/micho/1712812)
* [docker compose on networking](https://docs.docker.com/compose/networking/)
* [docker on links](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/)
