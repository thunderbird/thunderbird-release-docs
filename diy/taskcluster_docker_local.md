## Inspect a docker container used by taskcluster

These steps are based on the `build-linux64-aarch64/opt` job.

Take a look at the log in treeherder to find the image in use. For example:
```
Image 'public/image.tar.zst' from task 'D8EUWv8NQDq8NB-2ei-J8A' loaded.  Using image ID sha256:c74f7427dc1fc52a51b2f3bc4c23ff1f0fa305eca5127bf67804d9930e2ba888.
```

Also take a look at the steps in the log under:
```
=== Task Starting ===
```

Then download, setup, run, and drop into the shell of the container. For example:
```
TASK="D8EUWv8NQDq8NB-2ei-J8A"
SHA256="c74f7427dc1fc52a51b2f3bc4c23ff1f0fa305eca5127bf67804d9930e2ba888"
curl -L -o image.tar.zst https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/${TASK}/artifacts/public/image.tar.zst
zstd -d image.tar.zst
docker load -i image.tar
docker images --no-trunc | grep ${SHA256}
mkdir dirs
cd dirs/
mkdir -p checkouts tooltool-cache workspace
sudo chown -R 1000:1000 checkouts tooltool-cache workspace
docker run -it --rm -v "$(pwd)/checkouts:/builds/worker/checkouts" \
-v "$(pwd)/tooltool-cache:/builds/worker/tooltool-cache" \
-v "$(pwd)/workspace:/builds/worker/workspace" \
-e TASKCLUSTER_ROOT_URL=https://firefox-ci-tc.services.mozilla.com \
-u 1000:1000 sha256:${SHA256} /bin/bash
```
