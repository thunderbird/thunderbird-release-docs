# Trigger Bulk Actions On Taskcluster

1. Download the  [taskcluster client](https://github.com/taskcluster/taskcluster/tree/main/clients/client-shell#readme).  Install to location of your path and add to your path.
   ```sh
   curl -L https://github.com/taskcluster/taskcluster/releases/download/v96.4.0/taskcluster-linux-arm64.tar.gz --output taskcluster.tar.gz && tar -xvf taskcluster.tar.gz && rm taskcluster.tar.gz && chmod +x taskcluster
   ```
2.  Download the `tc-filter.py` script.  (or clone the entire braindump, if preferred)
   ```sh
   sudo wget https://hg.mozilla.org/build/braindump/raw-file/default/taskcluster/tc-filter.py -P /usr/local/bin/
sudo chmod +x /usr/local/bin/tc-filter.py
   ```
3. Configure `TASKCLUSTER_ROOT` to point to mozilla's instance.  Recommend adding the following line to your `.bashrc`
```sh
export TASKCLUSTER_ROOT_URL=https://firefox-ci-tc.services.mozilla.com
```
4. Sign into taskcluster.  This will create a short lived client with your credentials and pass back a token which will be stored in environment variables
```sh
eval $(taskcluster signin)
```
5.  Now the bootstrapping is done, and you can rerun tasks as needed. You can get `graph-id` from the taskcluster url. (eg https://firefox-ci-tc.services.mozilla.com/tasks/groups/KRYQ42TsSki2Dc2_OtOHIg -> KRYQ42TsSki2Dc2_OtOHIg)
```
tc-filter.py --state <failed|exception> --action rerun --graph-id <graph_id>
```