---
layout: post
title: SSH for Web Developers
---

SSH is network protocol designed for remote operating system control and tunneling. Data sent over SSH is encrypted.

Usually the client software for connection with remote server over SSH in Linux-like operating systems is pre-installed and called ssh. For Windows one can use, for instance, PuTTY.

In order to connect with remote server, type in your terminal `ssh example.com`

Port 22 and current logged on username will be used by default. Let's say you're logged in on your Linux box under roman username, and in this case command `ssh example.com` will be the same as `ssh roman@example.com -p 22`

### SSH Keys

Regardless of the fact that usually for remote sever connection one can use login/password pair, more often SSH-keys based authorization is used.

SSH-key is two files: `id_rsa.pub` (public part of the key) and `id_rsa` (private part of the key). To create your own SSH-key on Linux or Mac just type in your terminal `ssh-keygen`. By default on Linux or Mac the key is stored in `~/.ssh` directory. If someone's asking for a key to set up SSH access for you, you just need to send over the public key only (`id_rsa.pub`). Keep your private key private.

Let's take a look at example and see how SSH-based authorization works. Let's say we have Bob and Alice. Alice wants to send Bob a package, but at the same time she wants to protect her package from post-office workers, so the Bob only should be able to open it. Alice sends Bob a package with a lock on it. Post-office workers don't have a key from this lock, so they're not able to open the package. Bob is not able to open the package as well. So he puts his own lock on this package and sends the package back to Alice. Package gets delivered to Alice with two locks on it. Alice is able to remove only her own lock. She removes her lock and sends the package pack to Bob with only one lock left - Bob's lock. Bob has his own key from his own lock, so now he can open the package.

In this scenario nobody has sent his own keys, but the package was always protected with lock (or two locks). Locks here - our public digital keys. Keys - our private digital keys.

By using SSH with SSH-keys based authorization you won't need to type your server password. Moreover, you can specify multiple SSH keys for one user (in `~/.ssh/authorized_keys`). So that you can easily give an access to multiple users without revealing your password.

### SSH-client configuration file

Sometimes system administrators may change SSH port numbers, you may have different logins for two or more servers, etc. You can avoid typing ssh client configuration commands in your terminal by putting them in ssh client configuration file `~/.ssh/config`:

```
# help http://linux.die.net/man/5/ssh_config
#
Host example.com
User somebody
Port 2222
```

Now the command `ssh example.com` is the same as `ssh somebody@example.com -p 2222`

### SSH-tunneling

Let's imagine you need to run a query over the database on your server. But you want to do that from your own favorite fancy UI database client, without actually accessing the command-line of the remove server. Here SSH-tunneling comes into play. Command `ssh example.com -L 1111:192.168.0.2:2222` will redirect your local port with number 1111 to remote server with address 192.168.0.2 and port 2222. Now you can point your DB client app to localhost:1111.

### Creating alias for accessing the server

You can do the trick above with one command only. Create `~/.bash_profile` with this content:
```
example_server()
{
  ssh example.com -L 1111:192.168.0.2:2222
}
```

Then run `source ~/.bash_profile` or restart your terminal.

Now you can just type `example_server` and the tunnel will be created automatically. Creating aliases useful when you work with multiple servers or applications that require different port numbers.

### Copying files over SSH with scp

`scp` command can be used to copy files over SSH using the same authorization scheme as `ssh` command. For example, `scp index.html www@example.com:/home/www` will copy index.html to /home/www directory on example.com server.

#### See also

* [SSH on WikiPedia](https://en.wikipedia.org/wiki/Secure_Shell)
* [OpenSSH](http://www.openssh.com/)
* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
* [ssh man page](http://linux.die.net/man/1/ssh)
* [scp man page](http://linux.die.net/man/1/scp)
