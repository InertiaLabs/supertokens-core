## Setting up to build docker image
`git clone https://github.com/supertokens/supertokens-root.git`

update `modules.txt`
```txt
core,master,InertiaLabs
plugin-interface,master
postgresql-plugin,master
```

run `loadModules` (this may require some edits; I'm on Mac, and I had to update it):
```txt
diff --git a/loadModules b/loadModules
index 81fdde0..8789981 100755
--- a/loadModules
+++ b/loadModules
@@ -16,7 +16,7 @@ then
   url=git@bitbucket.org:vrai-labs
 fi
 
-if [ $prefix == "supertokens" ]; then
+if [[ $prefix == "supertokens" ]]; then
        url=https://github.com/
        if [[ $1 = "--ssh" ]]; then
                url=git@github.com:
@@ -24,7 +24,7 @@ if [ $prefix == "supertokens" ]; then
 fi
 
 # clear settings.gradle
-> settings.gradle
+echo '' > settings.gradle
 
 echo "include 'supertokens-core:cli'" >> settings.gradle
 echo "include 'supertokens-core:downloader'" >> settings.gradle
@@ -38,15 +38,15 @@ cat modules.txt | while read line
 do
        if [[ ! -z "$line" ]] && [[ $line != //* ]]
        then
-               while IFS=',' read -ra ADDR; do
+               while IFS=',' read -rA ADDR; do
                        counter=0
                        user_name=supertokens
                for i in "${ADDR[@]}"; do
-                               if [ $counter == 0 ];
+                               if [[ $counter == 0 ]];
                                 then
                                        repo_name="$i"
                                else
-                                       if [ $counter == 1 ];
+                                       if [[ $counter == 1 ]];
                                         then
                                                branch="$i"
                                        else
```
Update `docker/Dockerfile`:
```txt
diff --git a/docker/Dockerfile b/docker/Dockerfile
index d0ebb55..78efae2 100644
--- a/docker/Dockerfile
+++ b/docker/Dockerfile
@@ -30,6 +30,7 @@ RUN set -x \
        && apt-get purge -y --auto-remove ca-certificates wget
 COPY --from=tmp --chown=supertokens /usr/lib/supertokens /usr/lib/supertokens
 COPY --from=tmp --chown=supertokens /usr/bin/supertokens /usr/bin/supertokens
+RUN mkdir -p /usr/lib/supertokens/temp/webapps && chown supertokens:supertokens /usr/lib/supertokens/temp && chown supertokens:supertokens /usr/lib/supertokens/temp/webapps
 COPY docker-entrypoint.sh /usr/local/bin/
 RUN echo "$(md5sum /usr/lib/supertokens/config.yaml | awk '{ print $1 }')" >> /CONFIG_HASH
 RUN ln -s usr/local/bin/docker-entrypoint.sh /entrypoint.sh # backwards compat
 ```
 Run `docker/createPostgresqlImage`
 
## 📝 License

&copy; 2020-2023 SuperTokens Inc and its contributors. All rights reserved.

Portions of this software are licensed as follows:

* All content that resides under the "ee/" directory of this repository, if that directory exists, is licensed under the
  license defined in "ee/LICENSE.md".
* All third-party components incorporated into the SuperTokens Software are licensed under the original license provided
  by the owner of the applicable component.
* Content outside of the above-mentioned directories or restrictions above is available under the "Apache 2.0"
  license as defined in the level "LICENSE.md" file
