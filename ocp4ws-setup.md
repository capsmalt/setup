# 1) Add user into bastion-server & Create users.htpasswd [bastion-server]
STR=user
NUM=0
USERNAME=${STR}${NUM}
sudo useradd -p `perl -e "print(crypt('ocppass', 'bs'));"` $USERNAME
htpasswd -bc users.htpasswd $USERNAME ocppass

STR=user
NUM=1
for NUM in `seq 1 50`
do
  USERNAME=${STR}${NUM}
  sudo useradd -p `perl -e "print(crypt('ocppass', 'bs'));"` $USERNAME
  htpasswd -b users.htpasswd $USERNAME ocppass
done

# 2) Load users.htpasswd [OpenShift4 Console]
- [Administration]>[Cluster Settings]>[Global Configuration]>[OAuth]>[Add]>[HTPasswd]
	==> Load the users.htpasswd
- [Administration]>[Cluster Settings]>[Cluster Operators] 
	==> Wait for the "authentication" status to become "Available"
# 3) Grant the cluster-admin roll to each user
STR=user
NUM=0
for NUM in `seq 0 50`
do
  USERNAME=${STR}${NUM}
  oc adm policy add-cluster-role-to-user cluster-admin $USERNAME
done
