# VS Code

VS Code Remote-SSH continues to work. The one change is that you connect to a compute node only after you hold an allocation on it, whether that allocation is for a GPU or for CPU cores and memory.

## The pattern

1.  **Log in to the login node and request a node.**

    ```bash
    ssh <you>@sprlab005.csl.illinois.edu
    salloc --gres=gpu:1 --time=4:00:00
    ```

    `salloc` prints the node you were granted, for example `sprc01`. Keep this terminal open. Closing it, or letting the time run out, releases the node.

2.  **Point VS Code at that node.** Open the Remote Explorer, or run `Remote-SSH: Connect to Host`, and connect to the node name (for example, `sprc01`).

3.  **Work normally.** Open your folder, edit, run cells, and use the integrated terminal. During the optional period, `nvidia-smi` in the VS Code terminal lists all of the node's GPUs, so use only the ones you reserved.

4.  **When you finish,** close VS Code and run `exit` in the `salloc` terminal, or let the time expire, to return the node to the pool.

## Notes

-   **Allocate first, then connect.** Connecting VS Code to a node where you hold no allocation still works through soft enforcement on July 1, but at hard enforcement that connection is declined. Build the habit now: `salloc`, then connect. From July 1, an allocation sees only the GPUs it reserved.
-   **Keep the `salloc` terminal alive.** That terminal holds your allocation. If it closes, the node is released and your VS Code session loses access.
-   **Reuse one allocation.** You do not need a fresh allocation for each VS Code window. While the `salloc` is live, open as many VS Code or SSH sessions to that node as you like.
-   **Long runs do not need VS Code.** For multi-hour training, start the run inside `tmux` on the allocated node so it survives a sleeping laptop or a dropped connection, and reattach later with `tmux attach`. Use VS Code Remote-SSH for interactive development and debugging.

## Help

If you have trouble connecting, ask in `#server` and the sysadmin team will help you sort out your SSH configuration.
