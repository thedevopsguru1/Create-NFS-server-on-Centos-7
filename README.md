
# Setup External NSF SERVER
# CREATE CENTOS 7 NFS SERVER

## Installing the NFS server
```
sudo yum install -y nfs-utils
```
### Enable the server & start the server
```
sudo systemctl enable nfs-server
```
```
sudo systemctl start nfs-server
```
### Check the server status 
```
sudo systemctl status nfs-server
```
### create  directory if the pvc for k8s may be create  or create any other directory
```
 sudo mkdir /srv/nfs/kubedata -p
```
 ### change the directory owner to 
```
 sudo chown nfsnobody: /srv/nfs/kubedata/
```
 ## Let's edit the exports file to add to be exported to remote hosts
 ```
 sudo vi /etc/exports
 ```
 ```
 /srv/nfs/kubedata    *(rw,sync,no_subtree_check,no_root_squash,no_all_squash,insecure)
 ```
 #### Instead of * , you can put servers ip address
 
 ## Let's makje the local directory to be available for remote hosts:
 ```
 sudo exportfs -rav
 ```
 #### The output should look like this 
 #### exporting *:/srv/nfs/kubedata
 ## To have more details ,you can run 
 ```
 sudo exportfs -v
 ```
 ## To mount the NFS server to the remote hosts do ,
 ```
sudo mount -t nfs nfs_ip:/srv/nfs/kubedata /mnt
```
### Use this one
```
mount -t nfs -o sync 172.104.194.133:/srv/nfs/kubedata /mnt
```
```
sudo mount -t nfs nfs_ip:nfs_path host_client_path
```
# FIX ME
## To mount automatically 
### Please at how to automatically mount them


# If you want to unmount it do:
```
sudo umount host_client_path
```

 
 # To add the NSF to kubernetes pods
 ## 1- Configuration to add worker nodes to /etc/exports
 ### examples 
 ```
 /mnt/nfs_share  worker_node_01_ip_addr(rw,sync,no_subtree_check)
/mnt/nfs_share  worker_node_02_ip_addr(rw,sync,no_subtree_check)
/mnt/nfs_share  worker_node_03_ip_addr(rw,sync,no_subtree_check)
```
 ## 2- Mount using this example
 ```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: default
spec:
  restartPolicy: Never
  volumes:
  - name: nfs-vol
    nfs:
      path: <Mount point inside the NFS server>
      server: <NFS server ip address>
  containers:
  - name: nginx
    image: "nginx"
    volumeMounts:
    - name: nfs-vol
      mountPath: <Mount point inside the pod>
 ```
 
 ## These are other methodsof insataaling it to pods
 https://itnext.io/kubernetes-storage-part-1-nfs-complete-tutorial-75e6ac2a1f77
 
## adding privisoner k8s
##Use the first link:
https://itnext.io/kubernetes-storage-part-1-nfs-complete-tutorial-75e6ac2a1f77
https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner
## Install on the client
https://www.microhost.com/docs/tutorial/how-to-setup-nfs-server/

links:
https://shishirkh.medium.com/how-to-set-up-an-nfs-for-vms-kubernetes-6e6651d3d85b
https://www.exxactcorp.com/blog/Troubleshooting/deploying-dynamic-nfs-provisioning-in-kubernetes
https://shishirkh.medium.com/how-to-set-up-an-nfs-for-vms-kubernetes-6e6651d3d85b
