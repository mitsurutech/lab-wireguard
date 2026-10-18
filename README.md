# WireGuard lab: a VPN tunnel between two sites, in one Ubuntu VM

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-wireguard/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-wireguard/actions/workflows/check.yml)

A ready-made **WireGuard lab** for classrooms, from the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning), where schools and colleges give every student their own machines and terminal in the browser. Each student gets their own Ubuntu VM with two small sites built inside it, each with a gateway and a host, joined by an "internet" link that knows nothing of either private network. The student generates a **WireGuard key pair** on each gateway, writes `wg0.conf`, brings the **VPN tunnel** up and routes the two networks through it, until a host in one site can ping a host in the other. Nothing is installed on the student's computer.

A gateway needs a machine of its own and a student has one VM, so every host and gateway is a **network namespace** and every link a pair of virtual cables. The real commands work, which is the point: `wg genkey`, `wg pubkey`, `wg show`, `wg-quick`, `ip route`, `tcpdump`.

## The lab

| Lab | File | What the student does | Help page |
| --- | --- | --- | --- |
| WireGuard tunnel between two sites | [`wireguard.lab.yaml`](wireguard.lab.yaml) | Make a key pair on each gateway, keep the private keys private, write the configuration, bring the tunnel up, route the two LANs through it, watch the encrypted packets on the internet link, and make it survive a restart | [WireGuard lab](https://mitsurutech.co.uk/help/lab-wireguard) |

The lab leaves a README in the student's home folder that says what to do and gives none of the answers. It has a **check**, a script that marks a handed-in VM without the teacher reading it: it runs on a copy of the saved machine after a fresh start, so it sees only what the student saved in `wg0.conf`, and it passes only if the real state holds: a recent handshake on both gateways, the traffic of a ping moving through `wg0`, nothing but UDP on the internet link and no private address visible there, the private keys readable by nobody else, and no route that sends a private network over the internet link. A plain static route between the two networks does not pass.

## What is in the file

`wireguard.lab.yaml` is a whole **VM lab definition**, exactly what the platform runs:

| Key | What it is |
| --- | --- |
| `cpus`, `memory`, `disk` | the size of each student's VM: 1 CPU, 1024 MB and 10 GB |
| `open` | the ports that open in the browser: none, the lab is the terminal |
| `setup` | a script run once, as root, the first time each student's VM starts: it installs `wireguard-tools` and `tcpdump` from Ubuntu's archive, writes the `wglab` command, and the lab's README |
| `check` | the marking script, run as root on a copy of the saved disk after a fresh boot; its exit status is the result and `0` passes |

The `wglab` command it installs: `wglab status` lists the machines, `wglab shell gwA` opens a shell inside a gateway, and `wglab down` then `wglab up` rebuilds the lab and brings the tunnel up again from the saved files.

## Using it on the platform

**To set it as work for a class:**

1. In your module, add a task and choose the **Ubuntu VM lab** card.
2. Press the sample, or paste the contents of the file here. Using a sample replaces the text, and Undo brings yours back.
3. Publish and try your own copy first. Every student gets a VM of their own, kept between sessions.
4. Assign it like any task. When it is handed in, run the check on a student's row in the class list: it starts a copy of their saved machine that is not saved back, and shows whether it passed and what it printed.

The help manual's [guide to Ubuntu VM labs](https://mitsurutech.co.uk/help/labs) and its [sample labs page](https://mitsurutech.co.uk/help/sample-labs) cover the rest.

No college account yet? [Your first year is free](https://mitsurutech.co.uk/pricing) for up to 50 students, and [signing up](https://mitsurutech.co.uk/signup) takes an email address.

## Checking the file

On every push, `.github/workflows/check.yml` checks that the file is valid YAML with the five keys above and that `setup` and `check` are each inside the platform's 16 KB limit. It does not boot a VM: that is done on the platform's own machines.

## What does not work in a lab

The VM reaches Ubuntu's package archive, PyPI, npm and GitHub, and nothing else, so a lab cannot be pointed at a host on the internet. It is one VM, so the two sites are built inside it and the "internet" between them is a virtual link.

## About

Made by [Mitsuru Technologies](https://mitsurutech.co.uk) for the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning): Python, Java, Go, Node, SQL and Linux in the browser for schools and colleges, with container labs, VM labs, web apps deployed from GitHub, and quizzes that mark themselves. Try code with no account in the [playground](https://mitsurutech.co.uk/playground).

More labs from the same place: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin) and [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea).

## Licence

This work is licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). You may share it and adapt it for learning and teaching, if you credit Mitsuru Technologies Ltd, do not use it commercially, and share anything you make from it under the same licence. The full text is in the `LICENSE` file.

[WireGuard](https://www.wireguard.com) is GPL-2.0 licensed (the tools, and the module in the Ubuntu kernel) and is installed from Ubuntu's archive when a VM starts; nothing of it is distributed here.
