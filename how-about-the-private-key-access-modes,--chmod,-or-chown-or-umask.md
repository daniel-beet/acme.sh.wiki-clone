# How acme.sh deals with the private key file modes ?

A short answer is:  **we deal with the file permission as little as possible.**

A shorter answer is: **we almost don't change the file permissions, you set it yourself, and it will keep forever.**

Ok, let me give a longer answer:

1. By default, the key/cert files are saved in `~/.acme.sh`,  this folder is set to mode `700` by default. So, nobody else can read your private key.
2. When you use `--install-cert` command to **copy** the cert to the target locations,  we use `cat keyfile > target_key_file` pattern, in which the target file permission is not changed, and you only need **write** permission to the target file.
Yes, if the target file doesn't exist for the first time,  it will be created with your default *umask*, which is **umask 022** in most of the unix/linux systems.  In this case, somebody else *may* read your private key file.  But you can change the file mode manually, `chmod 700 target_key_file`.
*The reasons why we don't change the file mode are:*
    1. `chmod` may have different support crossing different platforms.  
    2. We respect the users choice most. We trust you more than ourselves.  you can change the file modes manually, you know your system best. We respect your choice.
    3. There may cause problems if running as root, for example, when running as root, and we default to change the mode to `700` for the private key file,  the apache server is commonly running as user `www-data`, it can not read your private key. nginx server has the same problem.  Which could cause the normal uses confused.


So, doing more doesn't mean doing better.   Less is more.






