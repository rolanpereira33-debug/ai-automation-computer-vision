---
icon: baby
---

# Guide for babies

**Open a terminal**&#x20;

Windows : press Win+R, type cmd, press Enter&#x20;

macOS: press Cmd+Space, type Terminal, press Enter&#x20;

Linux: Ctrl+Alt+T



**Create a project folder**

$  mkdir cv\_course

$  cd cv\_course



**Create a virtual environment**

<table data-header-hidden><thead><tr><th valign="top"></th></tr></thead><tbody><tr><td valign="top">$ python -m venv venv                    (Windows)</td></tr><tr><td valign="top">$ python3 -m venv venv                   (macOS / Linux)</td></tr><tr><td valign="top">You should now see a 'venv' folder inside cv_course.</td></tr></tbody></table>

**Activate the virtual environment**

<table data-header-hidden><thead><tr><th valign="top"></th></tr></thead><tbody><tr><td valign="top">$ .\venv\Scripts\activate               (Windows)</td></tr><tr><td valign="top">$ source venv/bin/activate               (macOS / Linux)</td></tr><tr><td valign="top">Your prompt should now start with (venv). If it does not, stop and ask for help.</td></tr></tbody></table>

**Install Packages**

<table data-header-hidden><thead><tr><th valign="top"></th></tr></thead><tbody><tr><td valign="top">$ pip install opencv-python numpy matplotlib</td></tr><tr><td valign="top">Wait for the downloads. You should see 'Successfully installed' at the end.</td></tr></tbody></table>
