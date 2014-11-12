#sftpclone

A tool for cloning/syncing a local directory tree with an SFTP server.

##Features

* Keep in sync a local directory tree with a specified folder of an SFTP server.
* Update symbolic links as needed and keep files _consistent_.
* Automatic tilde expansion/handling on the SFTP server.
* Public key authentication.
* ssh_config entries compatibility.
* Syncing exclusion patterns.
* Compatible with both Python 2 and Python 3.

##Usage

```
usage: sftpclone.py [-h] [-k private-key-path]
                    [-l {CRITICAL,ERROR,WARNING,INFO,DEBUG,NOTSET}] [-p PORT]
                    [-f] [-a] [-c ssh-config-path] [-e exclude-from-file-path]
                    local-path user[:password]@hostname:remote-path
```

Where, for each command line argument:

* **local-path**: The path of the local folder. This path must exists and can contain `~` (we use tilde expansion).
* **sftp-url**: It specifies the remote SFTP url having the form: `[user[:password]@]hostname:remote-path`. Both the password and the user field can be omitted. If you omit the former then you should specify a private key identity file. If you omit the latter then the current user is automatically used. The hostname can refer to a element of your `ssh_config` file. If the remote path contains `~`, then it will be expanded to the default folder in which the user begins her SFTP session.
* **[h]elp**: show the help message and exit.
* **private-[k]ey-path**: the path to your private identity file. Set it if you are not using password authentication. It automatically defaults to `~/.ssh/id_rsa`.
* **[l]ogging**: set the log level (ERROR by default).
* **[p]ort**: SSH remote port (defaults to 22).
* **[f]ix-symlinks**: if you have absolute symlinks pointing to your synced directory, they will remain consistent on the remote server: i.e., they will have an absolute path that reflect the path of the cloned directory on the server. Useful for cluster configurations.
* **ssh-[a]gent**: enable ssh-agent support. Any private-[k]ey-path argument will be ignored.
* **ssh-[c]onfig-path**: in the sftp-url's hostname you can [specify an entry of your `ssh_config` file](#ssh_config-compatibility). If you are using a non-standard path, you can set it here.
* **[e]xclude-from-file-path**: the path to a file containing a list of patterns. Each file matched by these pattern [will be ignored](#exclude-list) (not synced).

**Warning**: be sure to select a __proper__ remote folder. The syncronization process will indeed delete any file that doesn't exist in the local folder.

##ssh_config compatibility
The hostname in the sftp-url parameter can be a valid entry in a `ssh_config` file. Specifically, your entry should have relevant parameters such as:

* `Hostname`
* `Username`
* `Port`
* `IdentityFile`

Any value not found will fallback to the CLI arguments. 
Anyway, you _have to set_ the `IdentityFile` field, otherwise authentication will try to fallback to `~/.ssh/id_rsa` and could not work.
The first hostname matching the pattern is chosen (in the `ssh_config` way).

##Exclude list
It takes inspiration from the rsync/tar `--exclude-from` flag.

You can specify among your command line arguments a file containing a list of patterns, one per each line.
All those files that match any pattern will not be synced with the SFTP server.

Lines beginning with `;` or `#` are ignored.

Each pattern is considered relative to the syncing directory. As a consequence, leading `/` are ignored.

###Example
```ini
; This will exclude any file or directory beginning with foo
foo*
; This will exclude any file foo in a subdir of the directory bar.
bar/*/foo
```

##Testing
This project uses [nose](https://nose.readthedocs.org/en/latest/) for testing.

You can test it by launching from the project directory:
```bash
$ nosetests t
```
