[TODO]

Do not use `sudo` if you cannot properly configure it.

**Notice:** This wiki is not complete yet.

Using `sudo` is not recommended. If not properly configured to not ask for password it may cause permission issues when running commands from the cronjob (like renew), resulting in some or all of your certificates not being renewed and eventually will expire.

Now, if you are completely sure of the issues and the possibilities with the usage of `sudo` and still want to use it, you can pass the `--force` parameter.

**Remember:** Using `sudo` is not recommended. You have the option to force it at your own risk. You have been warned.