---
Order: 5
Area: advancedcontainers
TOCTitle: Change default mount
PageTitle: Change default source code mount in containers
ContentId: 1a1e7e2a-f483-47dd-b676-b75a11e4be1f
MetaDescription: Change default source code mount in containers
DateApproved: 9/20/2021
---
# Change the default source code mount

If you add the `image` or `dockerFile` properties to `devcontainer.json`, VS Code will automatically "bind" mount your current workspace folder into the container.  If `git` is present on the host's `PATH` and the folder containing `.devcontainer/devcontainer.json` is within a `git` repository, the current workspace mounted will be the root of the repository.  If `git` is not present on the host's `PATH`, the current workspace mounted will be the folder containing `.devcontainer/devcontainer.json`.

While this is convenient, you may want to change [mount settings](https://docs.docker.com/engine/reference/commandline/service_create/#add-bind-mounts-volumes-or-memory-filesystems), alter the type of mount, location, or [run in a remote container](#developing-inside-a-container-on-a-remote-docker-host).

You can use the `workspaceMount` property in `devcontainer.json` to change the automatic mounting behavior. It expects the same value as the [Docker CLI `--mount` flag](https://docs.docker.com/engine/reference/commandline/run/#add-bind-mounts-or-volumes-using-the---mount-flag).

For example:

```json
"workspaceMount": "source=${localWorkspaceFolder}/sub-folder,target=/workspace,type=bind,consistency=delegated",
"workspaceFolder": "/workspace"
```

This also allows you to do something like a named volume mount instead of a bind mount, which can be useful particularly when [using a remote Docker Host](#developing-inside-a-container-on-a-remote-docker-host) or you [want to store your entire source tree in a volume](#use-a-named-volume-for-your-entire-source-tree).

If you've already built the container and connected to it, run **Remote-Containers: Rebuild Container** from the Command Palette (`kbstyle(F1)`) to pick up the change. Otherwise run **Remote-Containers: Open Folder in Container...** to connect to the container.