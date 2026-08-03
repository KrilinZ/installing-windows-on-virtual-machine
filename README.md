<!-- hide -->
<div align="center">

# Installing Windows on a Virtual Machine

[![Certified tutorial](https://img.shields.io/badge/4Geeks-certified%20tutorial-2563eb)](https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine)
[![Autograded with LearnPack](https://img.shields.io/badge/LearnPack-autograded-2563eb)](https://github.com/learnpack/learnpack)
[![Open in Codespaces](https://img.shields.io/badge/Open%20in-Codespaces-fb5a1f)](https://codespaces.new/?repo=4GeeksAcademy/installing-windows-on-virtual-machine)

*These instructions are also [available in Spanish](https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine/blob/HEAD/README.es.md)*

</div>
<!-- endhide -->

Build your first security lab: this interactive tutorial installs Windows 10 inside a VirtualBox virtual machine in 5 guided steps — installing VirtualBox on Windows, Linux or Intel macOS, creating a Windows 10 ISO, and building the machine with an unattended install. The last step is autograded by 3 Jest assertions that check the guest is Windows 10 with at least 2048 MB of RAM and 2 CPUs.

<!-- hide -->
## 📋 About this tutorial

- **Difficulty:** easy (`"difficulty": "easy"` in [learn.json](https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine/blob/HEAD/learn.json))
- **Estimated duration:** 5 hours (`"duration": 5`)
- **Steps:** 5 sequential exercises, the last one autograded
- **Technologies:** cybersecurity, windows (`"technologies"` in learn.json)
- **Grading:** LearnPack + Jest, 3 assertions in a single test file
- **Languages:** English and Spanish, step by step
- **Where it runs:** GitHub Codespaces or a local LearnPack install (`"localhostOnly": true`)
- **Code you have to write:** none — you configure a virtual machine, not a program
<!-- endhide -->

## 🎯 What will you learn?

Virtualization is the first tool a security student sets up, because it is the only safe way to break things. By the end of this tutorial you will know how to:

- Install **VirtualBox** on Windows with the official wizard, on Debian-based Linux with a single `wget ... | sudo sh` command, on RPM distributions (Oracle Linux/RHEL, Fedora, openSUSE) by adding the Oracle repository, and on Intel-based macOS with the `.dmg` installer.
- Turn Microsoft's **Media Creation Tool** into an ISO file instead of a USB stick, choosing the architecture yourself instead of copying the host's configuration.
- Create a virtual machine in VirtualBox **Expert Mode**: name, operating system type, ISO image and virtual disk.
- Use **unattended installation** so VirtualBox types the whole Windows setup for you, including the generic product key that installs Windows without activating it.
- Size the guest hardware — CPU cores and base memory — so the virtual machine works well **without starving the host**.
- Read a machine's real configuration from the command line with `VBoxManage showvminfo --machinereadable`, which is how the tutorial proves your machine exists.

## 👀 What will you build?

A running Windows 10 virtual machine that VirtualBox reports as Windows 10, with at least 2 CPUs and 2048 MB of memory. You get there through 5 exercises inside the `exercises/` folder:

1. **`01-welcome`** — the roadmap: what a virtual lab is and why the worst thing that can happen inside it is having to reinstall the machine.
2. **`02-install-virtual-box`** — VirtualBox installation, with a separate path for Windows, Debian-based Linux, RPM-based Linux and Intel macOS.
3. **`03-download-windows-iso`** — downloading the Windows 10 media creation tool, accepting the terms, choosing "Create installation media", picking the architecture and saving an `.iso` file.
4. **`04-create-new-vm`** — creating the machine: OS type, ISO, unattended installation with a product key, at least 2 CPUs and 2 GB of memory, and the recommended storage configuration.
5. **`05-Verify-Installation`** — installing `curl`, publishing port `3001`, running the validation script and passing the tests.

The hardware step is the one that decides whether you pass. VirtualBox opens it with **1 CPU selected by default**, and that value fails the test:

![VirtualBox Create Virtual Machine wizard in Expert Mode with a Spanish interface, Hardware section expanded, showing the Base Memory (Memoria base) slider set to 2048 MB and the Processors (Procesadores) slider still at its default of 1 CPU, with the Finish (Terminar) and Cancel (Cancelar) buttons at the bottom](https://raw.githubusercontent.com/4GeeksAcademy/installing-windows-on-virtual-machine/master/.learn/assets/createvm3.png)

Move the Processors slider to 2 or more before clicking Finish. The tutorial's screenshots were taken on a Spanish VirtualBox, so those two controls appear there as *Procesadores* and *Terminar*.

## 🎓 What do you need before starting?

- **A computer where VirtualBox can be installed:** Windows, a Debian/Ubuntu or RPM-based Linux, or a Mac with an Intel CPU. The tutorial links the macOS installer for Intel processors only, so Apple Silicon machines are not covered.
- **Spare hardware for the guest:** at least 2 processor cores and 2048 MB of RAM that the host can lend to the virtual machine, plus room on disk for the Windows 10 ISO and the virtual hard drive.
- **Patience for two downloads:** the ISO is a large file, and the unattended installation then runs on its own until Windows boots.
- **`curl` on the machine that hosts VirtualBox**, because the validation script uses it to upload the report. Windows users can install it from [curl.se/windows](https://curl.se/windows/).
- **A GitHub account** if you want to run the exercises in Codespaces instead of installing LearnPack locally.
- **No programming experience.** There is nothing to code: the only test file is written for you.

## ✅ How does the automatic grading work?

Only the last exercise is graded, and it is graded against your real machine instead of against text you type. The chain has four links:

1. The Codespace runs `.devcontainer/postCreate.sh` when it starts, and that script launches `node server.js` — a small Express receiver that listens on the port set in `.env` (`HOST_PORT=3001`). LearnPack does not start it, so outside a Codespace you launch it yourself.
2. You make port `3001` public from the **PORTS** panel of your Codespace, so your own computer can reach it from outside.
3. You download and unzip the validation script — `sendDataWin.zip` for Windows, `sendDataBash.zip` for Linux — run it on the computer where VirtualBox lives, and paste the URL of port `3001` when it asks for it. The script runs `VBoxManage list vms`, dumps `VBoxManage showvminfo --machinereadable` for every machine into `data.txt` and uploads it with `curl -X POST -F "file=@data.txt"`.
4. The server parses that dump and writes `vminfo.json`, which the test reads.

Publishing the port is the step people skip. In the PORTS tab, right click the row for port `3001`, open **Port visibility** and choose **Public**:

![Visual Studio Code PORTS panel inside a GitHub Codespace, Spanish interface, showing the node_server entry on port 3001 and the right-click context menu open on the Port visibility (Visibilidad del puerto) submenu with the Public option selected](https://raw.githubusercontent.com/4GeeksAcademy/installing-windows-on-virtual-machine/master/.learn/assets/public-ports.png)

The test file [`exercises/05-Verify-Installation/test.js`](https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine/blob/HEAD/exercises/05-Verify-Installation/test.js) then runs 3 assertions against the machine whose `ostype` starts with `Windows 10`:

- The Windows 10 machine must exist in the report.
- Its `memory` value must be greater than or equal to `2048`.
- Its `cpus` value must be greater than or equal to `2`.

If `vminfo.json` is not there, the whole suite stops before the first assertion with the message `vminfo.json not found. Please run the BAT file on your local machine.`

## 💡 What mistakes should you avoid?

- **Running the validation script inside the Windows guest.** The script calls `VBoxManage`, which is part of VirtualBox and lives on the host, so it has to run on the computer where VirtualBox is installed.
- **Leaving the Processors slider at 1.** It is the default, and the third assertion asks for 2 or more. Base memory below 2048 MB fails in the same way.
- **Leaving port `3001` private.** The upload never arrives, `vminfo.json` is never written, and every test fails with the "not found" error even though your virtual machine is perfectly fine.
- **Choosing a different operating system type.** The report is matched against `Windows 10`, so a machine created as Windows 11 or as a generic "Other" type is not recognised.
- **Skipping the product key in the unattended installation.** The tutorial is explicit that leaving it empty causes errors during setup; the key it gives you installs Windows but does not activate it.
- **Running the script without unzipping it first,** or pasting a URL that is not the forwarded address of port `3001`.

## ❓ Frequently asked questions

### Do I need a Windows licence for this?

The unattended installation uses a generic product key that lets the installer finish but does not activate Windows. You end up with an unactivated Windows 10 for lab use, which is enough for the exercises and for the tests.

### Can I do this on a Mac with Apple Silicon (M1, M2, M3)?

Not with these instructions. The tutorial links the VirtualBox `.dmg` for Intel processors, and covers Windows and Linux hosts. On an Apple Silicon Mac you would need a different virtualization tool, which is outside the scope of these 5 steps.

### How much RAM and how many CPUs should I give the virtual machine?

At least 2048 MB and 2 CPUs, because those are the exact minimums the tests check. Going higher makes Windows smoother, but never give away so much that the host machine starts to struggle — the guest borrows the hardware, it does not add any.

### Do I have to write any code?

No. The exercises are configuration and screenshots; the test file is already written and the only thing you produce is a working virtual machine and the report the script uploads.

### Why does my test say `vminfo.json not found`?

Because the server never received your report. The usual causes are port `3001` still being private, the script being run inside the guest instead of on the host, or the URL you pasted not matching the forwarded address of port `3001`.

### Can I do the tutorial without GitHub Codespaces?

Yes. Clone the repository, run LearnPack locally and start the receiver yourself with `node server.js` — outside a Codespace nothing launches it for you. It then listens on `HOST_PORT=3001` on your own machine, so the address you paste into the validation script is `localhost:3001` instead of a Codespaces URL.

<!-- hide -->
## 📝 Other interactive tutorials

- [Learn Python Interactively (beginner)](https://4geeks.com/en/interactive-exercise/python-beginner-exercises)
- [Master Python by practice (interactive)](https://4geeks.com/en/interactive-exercise/master-python-exercises)

## 🚀 How to start

The fastest way is [opening the repository in Codespaces](https://codespaces.new/?repo=4GeeksAcademy/installing-windows-on-virtual-machine). Once VS Code is open, the LearnPack exercises should start on their own; if they do not, type `learnpack start` in the terminal.

Remember that the virtual machine itself is created on **your own computer**, not inside the Codespace: the Codespace only hosts the instructions and the validation server.

## 💻 Local installation

1. Clone the repository ([how to clone a repository](https://4geeks.com/how-to/github-clone-repository)) and enter the folder:

   ```bash
   git clone https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine.git
   cd installing-windows-on-virtual-machine
   ```

2. Install LearnPack and the node compiler plugin globally (Node.js is required):

   ```bash
   npm i @learnpack/learnpack -g
   learnpack plugins:install @learnpack/node
   ```

3. Install the project dependencies and start the tutorial at the same level as `learn.json`:

   ```bash
   npm i
   learnpack start
   ```

4. For the last exercise, open a second terminal and start the validation receiver, which nothing launches for you outside a Codespace:

   ```bash
   node server.js
   ```

## 📚 How the exercises are organized

- `exercises/` contains the 5 numbered folders, and LearnPack walks them in order.
- Every folder has a `README.md` and a `README.es.md`, so you can switch language at any step.
- Only `05-Verify-Installation` has a `test.js`; the other four are reading and doing.
- `learn.json` holds the tutorial configuration: title, description, duration, difficulty and technologies.
- `server.js` is the Express receiver that turns the VirtualBox dump into `vminfo.json`.
- `.learn/assets/` stores the screenshots and the two validation scripts, packed as `sendDataWin.zip` and `sendDataBash.zip`.

## 🤝 Contributors

Thanks to these wonderful people:

1. [Arnaldo Perez (arnaldoperez)](https://github.com/arnaldoperez), contribution: build-tutorial, documentation
2. [Alejandro Sánchez (alesanchezr)](https://github.com/alesanchezr), contribution: bug reports
3. [Lorena Gubaira (lorenagubaira)](https://github.com/lorenagubaira), contribution: bug reports, editor, translation

See the full list on the [contributors graph](https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine/graphs/contributors). Found a bug or a typo? [Open an issue](https://github.com/learnpack/learnpack/issues/new) — these exercises are built and maintained together with people like you.

This and many other interactive tutorials are built by 4Geeks Academy students and mentors, and used in the [Cybersecurity Bootcamp](https://4geeksacademy.com/us/coding-bootcamps/cybersecurity).
<!-- endhide -->
