## Manually run release promotion

This is useful when the push or ship stages of release promotion need to be rerun.

**Rerunning the `push_thunderbird` task**

1.  Go to the push on Treeherder where you'd like to rerun `push_thunderbird`
2.  Open the `push_thunderbird` attempt you'd like to rerun in Taskcluster
3.  Grab input from Full Task Definition of previous `push_thunderbird` attempt, which can be found under:

    ```
      action:
        context:
          input:
    ```

4.  Replace any `null` values with empty strings
5.  Return to Treeherder
6.  Open the `Action menu` drop-down menu in the top-right corner of the push you are working on
7.  Select `Custom Push Action...`
8.  Change the `Action` drop-down menu to `release-promotion`
9.  Paste the input you found and prepared in steps 3 and 4
10. Click `Trigger`