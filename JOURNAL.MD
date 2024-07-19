# Engine Journal

While the [console](CONSOLE) is reserved for user-to-app communication, the
engine journal is used for the engine to output various information to the user.
The journal is significantly more verbose as its primary purpose is to aid in
debugging. In case a fatal error occurs, the engine will automatically save the
journal to a file. You can then look through this file to troubleshoot yourself
or you can send it off to the app or engine developer for investigation. You can
view the journal at any moment by using the [`e.journal`](CONSOLE#ejournal)
console command.

<figure>
  <img src="img/journal.png" alt="Screenshot of the Journal window" />
  <figcaption>
    The in-engine journal window.
  </figcaption>
</figure>

All of the journal entries are broken up into pages. These entries are then
displayed in a table:

* **Time** is the timestamp since engine initialization at which the entry was
  added to the journal.
* **Category** is which component of the engine the entry pertains to. This
  includes, but is not limited to: "Renderer", "State manager", "Tick system"
  and "Miscellaneous".
* **Message** is a short description of the event that took place, whereas
* **Description** is a detailed description of the event and possibly all
  information related to it.

The **Save journal to disk** button allows you to save the journal into nwge's
data directory as a simple text file.

<figure>
  <img src="img/journalSaved.jpg" alt="Screenshot of the Journal info box." />
  <figcaption>
    The message displaying where the engine journal file is saved.
  </figcaption>
</figure>

This file contains the journal in plain text, which should make it easily
readable for post-cash debugging.

```log
0.000004  DEBUG/State   : Pre-initializing engine
Version: 0.13 (build #2326: 20 Jul 2024 at 01:59)

0.000274  DEBUG/System  : System Information
OS: EndeavourOS (like arch, kernel: Linux 6.9.10-arch1-1)
Debugger present: true
Memory usage: 184324 kB

0.001256  DEBUG/Misc.   : PathMan initialized #1
Root: /home/qeaml/.local/share/nwge
Store: /home/qeaml/.local/share/nwge/store/LOST+FOUND
Global Config: /home/qeaml/.local/share/nwge/config.json
App Config: 
GUI Config: /home/qeaml/.local/share/nwge/imgui.ini

0.338065  DEBUG/Misc.   : SDL Initialized
Headers version: 2.30.5
Binaries version: 2.30.5
```
