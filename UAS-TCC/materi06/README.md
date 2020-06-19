Kubernetes untuk Pemula
Apa saja terminal ini di browser?
Di sebelah kanan Anda akan melihat dua jendela terminal. Anda mungkin diminta masuk terlebih dahulu, yang bisa Anda lakukan dengan Docker ID atau akun GitHub. Terminal tersebut adalah terminal yang berfungsi penuh yang menjalankan Centos. Sebenarnya, mereka adalah commandline untuk wadah Centos yang berjalan di infrastruktur Play-with-Kubernetes kami. Ketika Anda melihat blok kode yang terlihat seperti ini, Anda bisa mengkliknya dan mereka akan terisi secara otomatis, atau Anda dapat menyalin dan menempelkannya.

Apa aplikasi ini?
Ini adalah penambang DockerCoin! 💰🐳📦🚢

Tidak, Anda tidak dapat membeli kopi dengan DockerCoins

Cara kerja DockerCoins:

pekerja meminta rng untuk menghasilkan beberapa byte acak

pekerja memasukkan byte ini ke dalam hasher

dan ulangi selamanya!

setiap detik, pembaruan pekerja redis untuk menunjukkan berapa banyak loop dilakukan

webui meminta redis, dan menghitung dan mengekspos "kecepatan hashing" di browser Anda

Mendapatkan kode sumber aplikasi
Kami telah membuat contoh aplikasi untuk dijalankan di sebagian bengkel. Aplikasi ini ada dalam repositori dockercoins.

Mari kita lihat tata letak umum dari kode sumber:

ada file Tulis docker-compose.yml…

… Dan 4 layanan lainnya, masing-masing dalam direktori sendiri:

rng = layanan web menghasilkan byte acak

hasher = hash komputasi layanan web dari data POSTed

pekerja = proses latar belakang menggunakan rng dan hasher

webui = antarmuka web untuk melihat kemajuan

Kami akan mengkloning repositori GitHub

Repositori juga berisi skrip dan alat yang akan kami gunakan melalui workshop
Banyak log
Aplikasi terus menerus menghasilkan log

Kita dapat melihat layanan pekerja membuat permintaan ke rng dan hasher

Mari kita letakkan itu di latar belakang

Menghubungkan ke UI web
Wadah webui memperlihatkan dasbor web; mari kita melihatnya

Dengan browser web, sambungkan ke node1 di sini pada port 8000 (dibuat saat Anda menjalankan aplikasi)

Membersihkan
Sebelum melanjutkan, mari matikan semuanya dengan mengetikkan Ctrl-C.

Konsep Kubernetes
Kubernetes adalah sistem manajemen wadah

Ini berjalan dan mengelola aplikasi kemas pada sebuah cluster

Apa maksudnya itu?

Hal-hal dasar yang dapat kita minta dari Kubernet
Mulai 5 kontainer menggunakan image atseashop / api: v1.3

Tempatkan penyeimbang beban internal di depan wadah ini

Mulai 10 wadah menggunakan atseashop / webfront gambar: v1.3

Tempatkan penyeimbang beban publik di depan wadah ini

Ini Black Friday (atau Natal), lonjakan lalu lintas, tumbuhkan cluster kami dan tambahkan kontainer

Rilis baru! Ganti wadah saya dengan gambar baru atseashop / webfront: v1.4

Pertahankan permintaan pemrosesan selama peningkatan; perbarui wadah saya satu per satu
Hal-hal lain yang bisa dilakukan Kubernet untuk kita
Autoscaling dasar

Biru / hijau, penyebaran kenari

Layanan berjalan lama, tetapi juga pekerjaan batch (sekali saja)

Komitmen berlebihan pada kluster kami dan usir pekerjaan prioritas rendah

Jalankan layanan dengan data stateful (database dll.)

Kontrol akses berbutir halus menentukan apa yang dapat dilakukan oleh siapa pada sumber daya mana

Mengintegrasikan layanan pihak ketiga (katalog layanan)

Mengotomatiskan tugas-tugas kompleks (operator)

Arsitektur Kubernetes
Arsitektur Kubernetes: master
Logika Kubernetes ("otak" -nya) adalah kumpulan layanan:

server API (titik masuk kami ke semuanya!)
layanan inti seperti manajer penjadwal dan pengontrol
etcd (toko kunci / nilai yang sangat tersedia; "database" Kubernetes)
* Bersama-sama, layanan ini membentuk apa yang disebut "master"

Layanan ini dapat berjalan langsung pada host, atau dalam wadah (itu detail implementasi)

etcd dapat dijalankan pada mesin yang terpisah (skema pertama) atau co-located (skema kedua)

Kami membutuhkan setidaknya satu master, tetapi kami dapat memiliki lebih banyak (untuk ketersediaan tinggi)

Arsitektur Kubernetes: node
Node yang mengeksekusi kontainer kami menjalankan koleksi layanan lain:

Engine wadah (biasanya Docker)
kubelet ("agen simpul")
kube-proxy (komponen jaringan yang diperlukan tetapi tidak cukup)
Node sebelumnya disebut "pelayan"

Merupakan kebiasaan untuk tidak menjalankan aplikasi pada node yang menjalankan komponen master (Kecuali ketika menggunakan cluster pengembangan kecil)

Sumber daya Kubernetes
API Kubernetes mendefinisikan banyak objek yang disebut sumber daya

Sumber daya ini disusun berdasarkan jenis, atau Jenis (dalam API)

Beberapa jenis sumber daya umum adalah:

simpul (mesin - fisik atau virtual - di cluster kami)
pod (sekelompok kontainer yang berjalan bersama pada sebuah node)
layanan (titik akhir jaringan yang stabil untuk terhubung ke satu atau beberapa kontainer)
namespace (kelompok hal yang kurang lebih terisolasi)
rahasia (bundel data sensitif untuk diteruskan ke wadah)
Dan banyak lagi! (Kita dapat melihat daftar lengkap dengan menjalankan get kubectl)

Deklaratif vs imperatif
Orkestra kontainer kami sangat menekankan deklaratif

Deklaratif:

Saya mau secangkir teh.

Imperatif:

Rebus air. Tuangkan dalam teko. Tambahkan daun teh. Curam sebentar. Sajikan dalam cangkir.

Deklaratif tampaknya lebih sederhana pada awalnya ...

... Selama kamu tahu cara menyeduh teh

Akan seperti apa deklaratif itu:
Saya ingin secangkir teh, diperoleh dengan menuangkan infus daun teh ke dalam cangkir.

Infus diperoleh dengan membiarkan benda curam beberapa menit dalam air panas.

Cairan panas diperoleh dengan menuangkannya dalam wadah yang sesuai dan meletakkannya di atas kompor.

Ah, akhirnya, wadah! Sesuatu yang kita ketahui. Ayo mulai bekerja, oke?
Ringkasan deklaratif vs imperatif
Sistem imperatif:

lebih sederhana

jika suatu tugas terganggu, kita harus memulai kembali dari awal

Sistem deklaratif:

jika tugas terganggu (atau jika kami muncul di pesta setengah jalan), kami dapat mencari tahu apa yang hilang dan hanya melakukan apa yang perlu

kita harus bisa mengamati sistem

... dan menghitung "perbedaan" antara apa yang kita miliki dan apa yang kita inginkan

Deklaratif vs imperatif di Kubernetes
Hampir semua yang kita buat di Kubernetes dibuat dari spec

Tonton bidang spesifikasi di file YAML nanti!

Spesifikasi tersebut menjelaskan bagaimana kita menginginkannya

Kubernetes akan merekonsiliasi kondisi saat ini dengan spek (secara teknis, ini dilakukan oleh sejumlah pengendali)

Ketika kami ingin mengubah beberapa sumber daya, kami memperbarui spesifikasi

Kubernetes kemudian akan menyatukan sumber daya itu

Model jaringan Kubernetes
TL, DR:

Cluster kami (node ​​dan pod) adalah satu jaringan IP flat besar.

Secara terperinci:

semua node harus dapat mencapai satu sama lain, tanpa NAT

semua pod harus dapat saling menjangkau, tanpa NAT

pod dan node harus dapat mencapai satu sama lain, tanpa NAT

setiap pod mengetahui alamat IP-nya (tidak ada NAT)

Kubernetes tidak mengamanatkan implementasi khusus apa pun

Model jaringan Kubernetes: yang baik
Semuanya bisa mencapai segalanya

Tidak ada terjemahan alamat

Tidak ada terjemahan port

Tidak ada protokol baru

Pod tidak dapat berpindah dari satu node ke node lain dan menjaga alamat IP-nya

Alamat IP tidak harus “portabel” dari satu simpul ke simpul lainnya (Kita dapat menggunakan misalnya subnet per simpul dan menggunakan topologi yang dialihkan sederhana)

Spesifikasi ini cukup sederhana untuk memungkinkan berbagai implementasi

Model jaringan Kubernetes: semakin kurang bagus
Semuanya bisa mencapai segalanya

jika Anda menginginkan keamanan, Anda perlu menambahkan kebijakan jaringan

implementasi jaringan yang Anda gunakan perlu mendukung mereka

Ada lusinan implementasi di luar sana (15 tercantum dalam dokumentasi Kubernetes)

Sepertinya Anda memiliki jaringan level 3, tetapi ini hanya level 4 (Spesifikasi ini membutuhkan UDP dan TCP, tetapi tidak rentang port atau paket IP sewenang-wenang)

kube-proxy berada di jalur data saat menyambungkan ke pod atau wadah, dan itu tidak terlalu cepat (bergantung pada proxy atau iptables userland)

Model jaringan Kubernetes: dalam praktiknya
Node yang kami gunakan telah diatur untuk menggunakan Weave

Kami tidak menganjurkan Weave dengan cara tertentu, itu hanya Berfungsi Untuk Kami

Jangan khawatir tentang peringatan tentang kinerja proksi kubus

Kecuali kamu:

secara rutin menjenuhkan antarmuka jaringan 10G

hitung tarif paket dalam jutaan per detik

menjalankan VOIP lalu lintas tinggi atau platform game

lakukan hal-hal aneh yang melibatkan jutaan koneksi simultan (dalam hal ini Anda sudah terbiasa dengan penyetelan kernel)
Kontak pertama dengan kubectl
kubectl adalah (hampir) satu-satunya alat yang kita perlukan untuk berbicara dengan Kubernetes

Ini adalah alat CLI yang kaya di sekitar API Kubernetes (Semua yang dapat Anda lakukan dengan kubectl, Anda dapat melakukannya langsung dengan API)

Anda juga dapat menggunakan flag --kubeconfig untuk meneruskan file konfigurasi

Atau langsung --server, --user, dll.

kubectl dapat diucapkan "Cube C T L", "Cube cuttle", "Cube cuddle" ...

dapatkan kubectl
Mari kita lihat sumber Node kami dengan get kubectl!

Lihatlah komposisi cluster kami:
Running our first containers on Kubernetes
First things first: we cannot run a container

We are going to run a pod, and in that pod there will be a single container

In that container in the pod, we are going to run a simple ping command

Then we are going to start additional copies of the pod

Starting a simple pod with kubectl run
We need to specify at least a name and the image we want to use

Let’s ping 8.8.8.8, Google’s public DNS

kubectl run pingpong --image alpine ping 8.8.8.8
OK, what just happened?

Behind the scenes of kubectl run
Let’s look at the resources that were created by kubectl run

List most resource types:

kubectl get all
We should see the following things:

deploy/pingpong (the deployment that we just created)
rs/pingpong-xxxx (a replica set created by the deployment)
po/pingpong-yyyy (a pod created by the replica set)
What are these different things?
A deployment is a high-level construct

allows scaling, rolling updates, rollbacks

multiple deployments can be used together to implement a canary deployment

delegates pods management to replica sets

A replica set is a low-level construct

makes sure that a given number of identical pods are running

allows scaling

rarely used directly

A replication controller is the (deprecated) predecessor of a replica set

Our pingpong deployment
kubectl run created a deployment, deploy/pingpong

That deployment created a replica set, rs/pingpong-xxxx

That replica set created a pod, po/pingpong-yyyy

We’ll see later how these folks play together for:

scaling

high availability

rolling updates

Viewing container output
Let’s use the kubectl logs command

We will pass either a pod name, or a type/name (E.g. if we specify a deployment or replica set, it will get the first pod in it)

Unless specified otherwise, it will only show logs of the first container in the pod (Good thing there’s only one in ours!)

View the result of our ping command:

kubectl logs deploy/pingpong
Streaming logs in real time
Just like docker logs, kubectl logs supports convenient options:

-f/--follow to stream logs in real time (à la tail -f)

--tail to indicate how many lines you want to see (from the end)

--since to get logs only after a given timestamp

View the latest logs of our ping command:

kubectl logs deploy/pingpong --tail 1 --follow
Scaling our application
We can create additional copies of our container (or rather our pod) with kubectl scale

Scale our pingpong deployment:

kubectl scale deploy/pingpong --replicas 8
Note: what if we tried to scale rs/pingpong-xxxx? We could! But the deployment would notice it right away, and scale back to the initial level.

Resilience
The deployment pingpong watches its replica set

The replica set ensures that the right number of pods are running

What happens if pods disappear?

In a separate window, list pods, and keep watching them:
kubectl get pods -w
Ctrl-C to terminate watching.

If you wanted to destroy a pod, you would use this pattern where yyyy was the identifier of the particular pod:
kubectl delete pod pingpong-yyyy
What if we wanted something different?
What if we wanted to start a “one-shot” container that doesn’t get restarted?

We could use kubectl run --restart=OnFailure or kubectl run --restart=Never

These commands would create jobs or pods instead of deployments

Under the hood, kubectl run invokes “generators” to create resource descriptions

We could also write these resource descriptions ourselves (typically in YAML), and create them on the cluster with kubectl apply -f (discussed later)

With kubectl run --schedule=…, we can also create cronjobs

Viewing logs of multiple pods
When we specify a deployment name, only one single pod’s logs are shown

We can view the logs of multiple pods by specifying a selector

A selector is a logic expression using labels

Conveniently, when you kubectl run somename, the associated objects have a run=somename label

View the last line of log from all pods with the run=pingpong label:

kubectl logs -l run=pingpong --tail 1
Unfortunately, --follow cannot (yet) be used to stream the logs from multiple containers.

Clean-up
Clean up your deployment by deleting pingpong

kubectl delete deploy/pingpong
Exposing containers
kubectl expose creates a service for existing pods

A service is a stable address for a pod (or a bunch of pods)

If we want to connect to our pod(s), we need to create a service

Once a service is created, kube-dns will allow us to resolve it by name (i.e. after creating service hello, the name hello will resolve to something)

There are different types of services, detailed on the following slides:

ClusterIP, NodePort, LoadBalancer, ExternalName

Basic service types
ClusterIP (default type)

a virtual IP address is allocated for the service (in an internal, private range) this IP address is reachable only from within the cluster (nodes and pods) our code can connect to the service using the original port number

NodePort

a port is allocated for the service (by default, in the 30000-32768 range) that port is made available on all our nodes and anybody can connect to it our code must be changed to connect to that new port number

These service types are always available.

Under the hood: kube-proxy is using a userland proxy and a bunch of iptables rules.

More service types
LoadBalancer
an external load balancer is allocated for the service
the load balancer is configured accordingly (e.g.: a NodePort service is created, and the load balancer sends traffic to that port)
ExternalName

the DNS entry managed by kube-dns will just be a CNAME to a provided record
no port, no IP address, no nothing else is allocated
Running containers with open ports
Since ping doesn’t have anything to connect to, we’ll have to run something else

Start a bunch of ElasticSearch containers:
kubectl run elastic --image=elasticsearch:2 --replicas=4
Watch them being started:

kubectl get pods -w
The -w option “watches” events happening on the specified resources.

Note: please DO NOT call the service search. It would collide with the TLD.

Exposing our deployment
We’ll create a default ClusterIP service

Expose the ElasticSearch HTTP API port:

kubectl expose deploy/elastic --port 9200
Look up which IP address was allocated:

kubectl get svc
Services are layer 4 constructs
You can assign IP addresses to services, but they are still layer 4 (i.e. a service is not an IP address; it’s an IP address + protocol + port)

This is caused by the current implementation of kube-proxy (it relies on mechanisms that don’t support layer 3)

As a result: you have to indicate the port number for your service

Running services with arbitrary port (or port ranges) requires hacks (e.g. host networking mode)

Testing our service
We will now send a few HTTP requests to our ElasticSearch pods

Let’s obtain the IP address that was allocated for our service, programatically:

IP=$(kubectl get svc elastic -o go-template --template '{{ .spec.clusterIP }}')
Send a few requests:

curl http://$IP:9200/
Our requests are load balanced across multiple pods.

Clean up
We’re done with the elastic deployment, so let’s clean it up

kubectl delete deploy/elastic
Our app on Kube
What’s on the menu?
In this part, we will:

build images for our app,

ship these images with a registry,

run deployments using these images,

expose these deployments so they can communicate with each other,

expose the web UI so we can access it from outside.

The plan
Build on our control node (node1)

Tag images so that they are named $USERNAME/servicename

Upload them to a Docker Hub

Create deployments using the images

Expose (with a ClusterIP) the services that need to communicate

Expose (with a NodePort) the WebUI

Setup
In the first terminal, set an environment variable for your Docker Hub user name. It can be the same Docker Hub user name that you used to log in to the terminals on this site.

export USERNAME=YourUserName
Make sure you’re still in the dockercoins directory.

pwd
A note on registries
For this workshop, we’ll use Docker Hub. There are a number of other options, including two provided by Docker.

Docker also provides:

Docker Trusted Registry which adds in a lot of security and deployment features including security scanning, and role-based access control.
Docker Open Source Registry.
Docker Hub
Docker Hub is the default registry for Docker.

Image names on Hub are just $USERNAME/$IMAGENAME or $ORGANIZATIONNAME/$IMAGENAME.

Official images can be referred to as just $IMAGENAME.

To use Hub, make sure you have an account. Then type docker login in the terminal and login with your username and password.

Using Docker Trusted Registry, Docker Open Source Registry is very similar.

Image names on other registries are $REGISTRYPATH/$USERNAME/$IMAGENAME or $REGISTRYPATH/$ORGANIZATIONNAME/$IMAGENAME.

Login using docker login $REGISTRYPATH.

Building and pushing our images
We are going to use a convenient feature of Docker Compose

Go to the stacks directory:

cd ~/dockercoins/stacks
Build and push the images:

docker-compose -f dockercoins.yml build
docker-compose -f dockercoins.yml push
Let’s have a look at the dockercoins.yml file while this is building and pushing.

version: "3"
services:
  rng:
    build: dockercoins/rng
    image: ${USERNAME}/rng:${TAG-latest}
    deploy:
      mode: global
  ...
  redis:
    image: redis
  ...
  worker:
    build: dockercoins/worker
    image: ${USERNAME}/worker:${TAG-latest}
    ...
    deploy:
      replicas: 10
Just in case you were wondering … Docker “services” are not Kubernetes “services”.

Deploying all the things
We can now deploy our code (as well as a redis instance)

Deploy redis:

kubectl run redis --image=redis
Deploy everything else:

for SERVICE in hasher rng webui worker; do
  kubectl run $SERVICE --image=$USERNAME/$SERVICE -l app=$SERVICE
done
Is this working?
After waiting for the deployment to complete, let’s look at the logs!

(Hint: use kubectl get deploy -w to watch deployment events)

Look at some logs:

kubectl logs deploy/rng
kubectl logs deploy/worker
🤔 rng is fine … But not worker.

💡 Oh right! We forgot to expose.

Exposing services
Exposing services internally
Three deployments need to be reachable by others: hasher, redis, rng

worker doesn’t need to be exposed

webui will be dealt with later

Expose each deployment, specifying the right port:

kubectl expose deployment redis --port 6379
kubectl expose deployment rng --port 80
kubectl expose deployment hasher --port 80
Is this working yet?
The worker has an infinite loop, that retries 10 seconds after an error

Stream the worker’s logs:

kubectl logs deploy/worker --follow
(Give it about 10 seconds to recover)

We should now see the worker, well, working happily.
Exposing services for external access
Now we would like to access the Web UI

We will expose it with a NodePort (just like we did for the registry)

Create a NodePort service for the Web UI:

kubectl create service nodeport webui --tcp=80 --node-port=30001
Check the port that was allocated:

kubectl get svc
Accessing the web UI
We can now connect to any node, on the allocated node port, to view the web UI
Click on this link

Alright, we’re back to where we started, when we were running on a single node!

Security implications of kubectl apply
When we do kubectl apply -f <URL>, we create arbitrary resources

Resources can be evil; imagine a deployment that …

starts bitcoin miners on the whole cluster

hides in a non-default namespace

bind-mounts our nodes’ filesystem

inserts SSH keys in the root account (on the node)

encrypts our data and ransoms it

☠️☠️☠️

kubectl apply is the new curl | sh
curl | sh is convenient

It’s safe if you use HTTPS URLs from trusted sources

kubectl apply -f is convenient

It’s safe if you use HTTPS URLs from trusted sources

It introduces new failure modes

Example: the official setup instructions for most pod networks

Scaling a deployment
We will start with an easy one: the worker deployment

kubectl get pods

kubectl get deployments
Now, create more worker replicas:

kubectl scale deploy/worker --replicas=10
After a few seconds, the graph in the web UI should show up. (And peak at 10 hashes/second, just like when we were running on a single one.)

Daemon sets
What if we want one (and exactly one) instance of rng per node?

If we just scale deploy/rng to 2, nothing guarantees that they spread

Instead of a deployment, we will use a daemonset

Daemon sets are great for cluster-wide, per-node processes:

kube-proxy
weave (our overlay network)
monitoring agents
hardware management tools (e.g. SCSI/FC HBA agents)
etc.
They can also be restricted to run only on some nodes.

Creating a daemon set
Unfortunately, as of Kubernetes 1.9, the CLI cannot create daemon sets

More precisely: it doesn’t have a subcommand to create a daemon set

But any kind of resource can always be created by providing a YAML description:

kubectl apply -f foo.yaml
How do we create the YAML file for our daemon set?

option 1: read the docs
option 2: vi our way out of it
Creating the YAML file for our daemon set
Let’s start with the YAML file for the current rng resource

Dump the rng resource in YAML:

kubectl get deploy/rng -o yaml --export >rng.yml
Edit rng.yml

Note: --export will remove “cluster-specific” information, i.e.:

namespace (so that the resource is not tied to a specific namespace) status and creation timestamp (useless when creating a new resource) resourceVersion and uid (these would cause… interesting problems)

“Casting” a resource to another
What if we just changed the kind field?

(It can’t be that easy, right?)

Change kind: Deployment to kind: DaemonSet

Save, quit

Try to create our new resource:

  kubectl apply -f rng.yml
We all knew this couldn’t be that easy, right!
Understanding the problem
The core of the error is:

error validating data:
[ValidationError(DaemonSet.spec):
unknown field "replicas" in io.k8s.api.extensions.v1beta1.DaemonSetSpec,
...
Obviously, it doesn’t make sense to specify a number of replicas for a daemon set

Workaround: fix the YAML

remove the replicas field
remove the strategy field (which defines the rollout mechanism for a deployment)
remove the status: {} line at the end
Or, we could also …

Use the --force, Luke
We could also tell Kubernetes to ignore these errors and try anyway

The --force flag actual name is --validate=false

Try to load our YAML file and ignore errors:

kubectl apply -f rng.yml --validate=false
Use the –force, Luke We could also tell Kubernetes to ignore these errors and try anyway

The –force flag actual name is –validate=false

Try to load our YAML file and ignore errors: kubectl apply -f rng.yml –validate=false 🎩✨🐇

Wait … Now, can it be that easy?

Checking what we’ve done
Did we transform our deployment into a daemonset?

Look at the resources that we have now:

kubectl get all
We have both deploy/rng and ds/rng now!

And one too many pods…

Explanation
You can have different resource types with the same name (i.e. a deployment and a daemonset both named rng)

We still have the old rng deployment

But now we have the new rng daemonset as well

If we look at the pods, we have:

one pod for the deployment
one pod per node for the daemonset
What are all these pods doing?
Let’s check the logs of all these rng pods

All these pods have a run=rng label:

the first pod, because that’s what kubectl run does
the other ones (in the daemon set), because we copied the spec from the first one
Therefore, we can query everybody’s logs using that run=rng selector

Check the logs of all the pods having a label run=rng:

kubectl logs -l run=rng --tail 1
It appears that all the pods are serving requests at the moment.

Removing the first pod from the load balancer
What would happen if we removed that pod, with kubectl delete pod ...?

The replicaset would re-create it immediately.

What would happen if we removed the run=rng label from that pod?

The replicaset would re-create it immediately.

… Because what matters to the replicaset is the number of pods matching that selector.

But but but … Don’t we have more than one pod with run=rng now?

The answer lies in the exact selector used by the replicaset …

Deep dive into selectors
Let’s look at the selectors for the rng deployment and the associated replica set

Show detailed information about the rng deployment:

kubectl describe deploy rng
Show detailed information about the rng replica:

kubectl describe rs rng-yyyy
The replica set selector also has a pod-template-hash, unlike the pods in our daemon set.

Updating a service through labels and selectors
What if we want to drop the rng deployment from the load balancer?

Option 1:

destroy it
Option 2:

add an extra label to the daemon set
update the service selector to refer to that label
Of course, option 2 offers more learning opportunities. Right?

Add an extra label to the daemon set
We will update the daemon set “spec”

Option 1:

edit the rng.yml file that we used earlier
load the new definition with kubectl apply
Option 2:

use kubectl edit
If you feel like you got this, feel free to try directly. We’ve included a few hints on the next slides for your convenience!

We’ve put resources in your resources
Reminder: a daemon set is a resource that creates more resources!

There is a difference between:

the label(s) of a resource (in the metadata block in the beginning)
the selector of a resource (in the spec block)
the label(s) of the resource(s) created by the first resource (in the template block)
You need to update the selector and the template (metadata labels are not mandatory)

The template must match the selector (i.e. the resource will refuse to create resources that it will not select)

Adding our label
Let’s add a label isactive: yes

In YAML, yes should be quoted; i.e. isactive: "yes"

Update the daemon set to add isactive: "yes" to the selector and template label:

kubectl edit daemonset rng
spec:
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: rng      
      isactive: "yes"
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: rng      
        isactive: "yes"
Update the service to add isactive: "yes" to its selector:

kubectl edit service rng
Checking what we’ve done
Check the logs of all run=rng pods to confirm that only 2 of them are now active:

kubectl logs -l run=rng
The timestamps should give us a hint about how many pods are currently receiving traffic.

Look at the pods that we have right now:

kubectl get pods
More labels, more selectors, more problems?
Bonus exercise 1: clean up the pods of the “old” daemon set

Bonus exercise 2: how could we have done this to avoid creating new pods?

Rolling updates
By default (without rolling updates), when a scaled resource is updated:

new pods are created
old pods are terminated
… all at the same time
if something goes wrong, ¯_(ツ)_/¯
With rolling updates, when a resource is updated, it happens progressively

Two parameters determine the pace of the rollout: maxUnavailable and maxSurge

They can be specified in absolute number of pods, or percentage of the replicas count

At any given time …

there will always be at least replicas-maxUnavailable pods available
there will never be more than replicas+maxSurge pods in total
there will therefore be up to maxUnavailable+maxSurge pods being updated
We have the possibility to rollback to the previous version (if the update fails or is unsatisfactory in any way)

Rolling updates in practice
As of Kubernetes 1.8, we can do rolling updates with:

deployments, daemonsets, statefulsets

Editing one of these resources will automatically result in a rolling update

Rolling updates can be monitored with the kubectl rollout subcommand

Building a new version of the worker service
Edit dockercoins/worker/worker.py, update the sleep line to sleep 1 second

Go to the stack directory:

cd stacks
Build a new tag and push it to the registry:

  export TAG=v0.2
  docker-compose -f dockercoins.yml build
  docker-compose -f dockercoins.yml push
Rolling out the new worker service
Let’s monitor what’s going on by opening a few terminals, and run:

  kubectl get pods -w
  kubectl get replicasets -w
  kubectl get deployments -w
Update worker either with kubectl edit, or by running:

kubectl set image deploy worker worker=$USERNAME/worker:$TAG
That rollout should be pretty quick. What shows in the web UI?

Rolling out an error
What happens if we make a mistake?

Update worker by specifying a non-existent image:

export TAG=v0.3
kubectl set image deploy worker worker=$REGISTRY/worker:$TAG
Check what’s going on:

kubectl rollout status deploy worker
Our rollout is stuck. However, the app is not dead (just 10% slower).

Recovering from a bad rollout
We could push some v0.3 image (the pod retry logic will eventually catch it and the rollout will proceed)

Or we could invoke a manual rollback

Cancel the deployment and wait for the dust to settle down:

kubectl rollout undo deploy worker
kubectl rollout status deploy worker
Changing rollout parameters
We want to:

revert to v0.1 (which we now realize we didn’t tag - yikes!)
be conservative on availability (always have desired number of available workers)
be aggressive on rollout speed (update more than one pod at a time)
give some time to our workers to “warm up” before starting more
The corresponding changes can be expressed in the following YAML snippet:

spec:
  template:
    spec:
      containers:
      - name: worker
        image: $USERNAME/worker:latest
  strategy:
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 3
  minReadySeconds: 10
Applying changes through a YAML patch
We could use kubectl edit deployment worker

But we could also use kubectl patch with the exact YAML shown before

Apply all our changes and wait for them to take effect:

kubectl patch deployment worker -p "
spec:
  template:
    spec:
      containers:
      - name: worker
        image: $USERNAME/worker:latest
  strategy:
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 3
  minReadySeconds: 10
"
kubectl rollout status deployment worker
Next steps
Alright, how do I get started and containerize my apps?

Suggested containerization checklist:

write a Dockerfile for one service in one app
write Dockerfiles for the other (buildable) services
write a Compose file for that whole app
make sure that devs are empowered to run the app in containers
set up automated builds of container images from the code repo
set up a CI pipeline using these container images
set up a CD pipeline (for staging/QA) using these images
And then it is time to look at orchestration!

Namespaces
Namespaces let you run multiple identical stacks side by side

Two namespaces (e.g. blue and green) can each have their own redis service

Each of the two redis services has its own ClusterIP

kube-dns creates two entries, mapping to these two ClusterIP addresses:

redis.blue.svc.cluster.local and redis.green.svc.cluster.local

Pods in the blue namespace get a search suffix of blue.svc.cluster.local

As a result, resolving redis from a pod in the blue namespace yields the “local” redis

This does not provide isolation! That would be the job of network policies.

Stateful services (databases etc.)
As a first step, it is wiser to keep stateful services outside of the cluster

Exposing them to pods can be done with multiple solutions:

ExternalName services (redis.blue.svc.cluster.local will be a CNAME record)

ClusterIP services with explicit Endpoints (instead of letting Kubernetes generate the endpoints from a selector)

Ambassador services (application-level proxies that can provide credentials injection and more)

Stateful services (second take)
If you really want to host stateful services on Kubernetes, you can look into:

volumes (to carry persistent data)
storage plugins
persistent volume claims (to ask for specific volume characteristics)
stateful sets (pods that are not ephemeral)
HTTP traffic handling
Services are layer 4 constructs

HTTP is a layer 7 protocol

It is handled by ingresses (a different resource kind)

Ingresses allow:

virtual host routing
session stickiness
URI mapping
and much more!
Logging and metrics
Logging is delegated to the container engine

Metrics are typically handled with Prometheus

Managing the configuration of our applications
Two constructs are particularly useful: secrets and config maps

They allow to expose arbitrary information to our containers

Avoid storing configuration in container images (There are some exceptions to that rule, but it’s generally a Bad Idea)

Never store sensitive information in container images (It’s the container equivalent of the password on a post-it note on your screen)

Cluster federation
Kubernetes master operation relies on etcd

etcd uses the Raft protocol

Raft recommends low latency between nodes

What if our cluster spreads to multiple regions?

Break it down in local clusters

Regroup them in a cluster federation

Synchronize resources across clusters

Discover resources across clusters

Developer experience
I’ve put this last, but it’s pretty important!

How do you on-board a new developer?

What do they need to install to get a dev stack?
How does a code change make it from dev to prod?
How does someone add a component to a stack?
