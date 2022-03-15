# ansible-vagrant


## Vagrant ile Kubernetes Kurulumu

1. Kubernetes Cluster ını ayağa kaldırmak için sırasıyla aşağıdaki komutları çalıştırıyoruz. (1 Master 1 Worker)

```bash
git clone https://github.com/eminturan/ansible-vagrant.git

cd ansible-vagrant/ansible-vagrant-kubernetes

vagrant up
```

2. Master makinesine login olup kurulumla birlikte ayağa kalkan mysql pod unu listeliyoruz.

```bash
vagrant ssh k8s-m-1

kubectl get pod -n dev
```

## Ansible ile Source Kodun Ortama Deploy Edilmesi

1. Source Kodu deploy etmek için ansible-helm-deployment folder ına geçiyoruz.

```bash
cd ansible-helm-deployment
```

2. external_vars.yml dosyasındaki docker_username, docker_password, image_url, image_version parametrelerini kendimize göre düzenliyoruz.

```bash
git_repo_url: "https://github.com/eminturan/python-example.git" #source code un bulunduğu repo
project: "dev" #deploy edilecek ortam
appName: "python-example" #servisin ismi

#Kendi dockerhub bilgilerinize göre güncelleyiniz.
docker_username: "username_giriniz"
docker_password: "password_giriniz"
image_url: "username_giriniz/example" #örnek, username/example
image_version: "python1" #örnek, versiyon giriniz
```

3. main.yml daki vars.ansible_ssh_private_key_file alanını aşağıdaki komutu çalıştırdığımızda dönen sonuçta "IdentityFile" parametresinin yanında yazan path ile düzenliyoruz. (Komutu ansible-vagrant-kubernetes klasörünün içinde çalıştırmanız gerekiyor.)

```bash
vagrant ssh-config k8s-m-1
```

main.yml
```bash
vars:
  ansible_ssh_private_key_file: "/Users/username/.vagrant.d/insecure_private_key"
```

4. Düzenlemeler bittikten sonra aşağıdaki komutu çalıştırıp kaynak kodu ortama deploy ediyoruz. Sırasıyla:
  - Kaynak kod repodan indiriliyor,
  - Docker login, build ve push işlemleri yapılıyor,
  - Helm ile ortama deploy ediliyor.

```bash
ansible-playbook main.yml -i hosts -b -v
```

5. Python kodunun ui çıktısına erişmek için service kısmından nodeport unu alıp 192.168.50.11:port port kısmına yazıyoruz.

```bash
kubectl get svc -n dev
```

Output:

![Arc](https://github.com/eminturan/ansible-vagrant/blob/main/ui-output.png)
