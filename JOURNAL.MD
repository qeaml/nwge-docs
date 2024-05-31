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
0.000002  DEBUG/Object  : Object created
Type: 0x1
Num: 0x0000

0.000083  DEBUG/State   : Pre-initializing engine
Version: 0.6 (build #11946: 27 Apr 2024 at 13:38)

0.000201  DEBUG/Misc.   : PathMan initialized #1
Root: /home/qeaml/.local/share/nwge
Store: /home/qeaml/.local/share/nwge/store/LOST+FOUND
Global Config: /home/qeaml/.local/share/nwge/config.json
App Config: 
GUI Config: /home/qeaml/.local/share/nwge/imgui.ini


0.179951  DEBUG/Misc.   : SDL Initialized
Headers version: 2.30.2
Binaries version: 2.30.2

0.179973  DEBUG/Misc.   : SDL_image Initialized
Binaries version: 2.8.2

0.180061  DEBUG/Misc.   : SDL_ttf Initialized
Headers version: 2.22.0
FreeType version: 2.13.2
HarfBuzz version: 8.4.0
```
