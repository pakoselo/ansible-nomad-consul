# The cloud is deployed as an CentOS 9 images and the scripts are CentOS ready
# Create own certificates, by executing these scripts. In case you don't have the cfssl library, install it by (on Ubuntu):
apt install golang-cfssl

cfssl print-defaults csr | cfssl gencert -initca - | cfssljson -bare nomad-ca

echo '{}' | cfssl gencert -ca=nomad-ca.pem -ca-key=nomad-ca-key.pem -config=cfssl.json -hostname="server.global.nomad,localhost,127.0.0.1" - | cfssljson -bare server
echo '{}' | cfssl gencert -ca=nomad-ca.pem -ca-key=nomad-ca-key.pem -config=cfssl.json -hostname="client.global.nomad,localhost,127.0.0.1" - | cfssljson -bare client
echo '{}' | cfssl gencert -ca=nomad-ca.pem -ca-key=nomad-ca-key.pem -profile=client - | cfssljson -bare cli

# Later copy generated files into "files" subfolder in the ansible-role-client-certificates and ansible-role-server-certificates. The plan is to automate this task in the future as the ansible ready tasks too.

# Other stuff is pretty the same for all ansible tasks. Edit the inventory file and replace the ip and keys with your own and execute the deploymet by:
ansible-playbook playbook.yml -i inventory
