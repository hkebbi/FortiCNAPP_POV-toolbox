

df -h $HOME
du -sh $HOME/* 2>/dev/null | sort -h | tail -n 20

When you’re using AWS CloudShell, your persistent directory is:

/home/cloudshell-user/


| Concept                        | Path                              | Purpose                                     |
| ------------------------------ | --------------------------------- | ------------------------------------------- |
| Your persistent home directory | `/home/cloudshell-user`           | Where configs and binaries are stored       |
| `$HOME` environment variable   | Points to `/home/cloudshell-user` | Shorthand for your home path                |
| Lacework CLI config folder     | `$HOME/lacework`                  | Stores your profile (API key, tenant, etc.) |


cd $HOME
--> It simply lists the folder /home/cloudshell-user/lacework — because $HOME points exactly there


~ $ mkdir forticnapp
~ $ ls
bin  forticnapp
~ $ cd forticnapp/
forticnapp $ ls
forticnapp $ mkdir cloud-api
forticnapp $ cd cloud-api/
cloud-api $ curl -H "Authorization: Bearer _5f67a3efcaf8b91ad7cd118147f7c995" https://316605.lacework.net/api/v2/Onboarding/b28667e6-aed4-47a5-a80c-f55883ae6890.sh > forticnapp.sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  4379    0  4379    0     0   9154      0 --:--:-- --:--:-- --:--:--  9141
cloud-api $ 
cloud-api $ ./forticnapp.sh
-bash: ./forticnapp.sh: Permission denied
cloud-api $ chmod +x forticnapp.sh 
cloud-api $ ./forticnapp.sh

Setting up your AWS Cloud Shell.
---> it will install Terraform , Forticnapp CLI and Configure

| Folder             | Path                               | Contains                                  | Persistent | Notes                                |
| ------------------ | ---------------------------------- | ----------------------------------------- | ---------- | ------------------------------------ |
| `$HOME/bin`        | `/home/cloudshell-user/bin`        | Executable binaries (lacework, terraform) | ✅ Yes      | Automatically loaded in `$PATH`      |
| `$HOME/lacework`   | `/home/cloudshell-user/lacework`   | Configs (like Terraform)                  | ✅ Yes      | Lacework CLI profile and tenant info |
| `$HOME/forticnapp` | `/home/cloudshell-user/forticnapp` | Your project/scripts                      | ✅ Yes      | Optional working folder              |
.lacework.toml
New Lacework CLI (v2.x+)
~/.lacework.toml
Stores the same profile info in TOML format
cat ~/.lacework.toml

❌ You cannot expand /home — AWS caps it at 1 GB.
✅ You can use /tmp + S3 (or EFS) to get effectively unlimited persistent space.



Cloud9 (you need EIP / IGW and no inbound access) --> Session Manager

install in your home directory instead
cd ~
curl -H "Authorization: Bearer _5f67a3efcaf8b91ad7cd118147f7c995" https://316605.lacework.net/api/v2/Onboarding/d007549a-802c-47e5-9fab-b8f8a6f3c756.sh > forticnapp.sh
chmod +x forticnapp.sh
./forticnapp.sh

Install terraform and Forticnapp CLI



