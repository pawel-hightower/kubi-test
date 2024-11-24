# kubi-test
Kubernetes środowisko testowe
Cel: przygotowanie środowiska testowego do nauki Kubernetesa, które będzie jak najwierniej symulowało małe środowisko produkcyjne.
Całe to zadanie nazywa się kubi-test.
To posiadam:
Hardware: laptop DELL XPS, processor Intel core i7-7700HQ, 32GB pamięci
Hyperwizor: VMWare Workstation.
Klient SSH: moba Xterm

Założenia podstawowe: Kubernetes musi chodzić w klastrze, więc potrzebne są co najmniej 3 węzły. Każdy węzeł z tego co czytałem powinien być na osobnej maszynie wirtualnej, więc powinienem utworzyć 3 maszyny. Podaj przykładowe parametry tych maszyn, uwzględniając też wymagania poniżej. Cały kod będzie przechowywany w GitHubie, więc niezbędne będzie podłączenie z jednego z węzłów do projektu GitHuba.
Etap 1:
Instalacja linuxa (Ubuntu 24.04) na nodach, instalacja kubernetesa, podłączenie do projektu github. To wykonam sam, na tym etapie nie potrzebuję pomocy (poza podaniem sugerowanych parametrów CPU/RAM dla maszyn wirtualnych).
Serwery zainstalowane. Skonfigurowany stały adres IP, logowanie po kluczu ssh. W /etc/hosts dodane hostname’y wszystkich nodów, żeby można było komunikować się z nimi po hostname.
Wykonane komendy apt update i apt upgrade.
Wyłączmy swap: sudo swapoff -a
Instalujemy Dockera: apt-get install docker.io -y
Dodanie klucza Google apt, żeby weryfikować wersję:
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add –
Instalujemy dodatkowe pakiety niezbędne do instalacji kubernetesa:
apt-get install -y apt-transport-https ca-certificates curl gpg
Tworzymy folder: mkdir -p -m 755 /etc/apt/keyrings
Dodajemy nowe repozytorium do pobrania kubernetesa i dodajemy klucz uwierzytelniający to repozytorium:
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
apt update
apt install -y kubelet kubeadm kubectl
Można zablokować automatyczną aktualizację wybranych pakietów:
sudo apt-mark hold kubelet kubeadm kubectl
Na master nodzie inicjujemy klaster: kubeadm init --pod-network-cidr=192.168.1.30/24 -v=9

Pod koniec wykonywania tej komendy dostaniemy następujący komunikat:

Your Kubernetes control-plane has initialized successfully!
To start using your cluster, you need to run the following as a regular user:
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
Alternatively, if you are the root user, you can run:
  export KUBECONFIG=/etc/kubernetes/admin.conf

Należy wykonać powyższe komendy.

Poniżej jest dalsza część komunikatu:
Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.1.30:6443 --token qoqxoq.5ordjp4fj7fgjt2t \
        --discovery-token-ca-cert-hash sha256:155f4dd54c772b6ef8fc50829f230821ca43938fe777b2e377baec77212a1de5

Tę komendę należy skopiować i wykonać na każdym worker nodzie, który ma się stać częścią klastra.


Etap 2:
Na środowisku chcę zainstalować co najmniej dwie niezależne aplikacje webowe, żeby trzeba było użyć ingressa, w tym co najmniej jedna używająca bazy danych, żeby dodatkowo tworzone były kapsuły bazodanowe. Podaj propozycje z ogólnie dostępnych, już skonfigurowanych, żeby działały od razu i od razu w przeglądarce można było cokolwiek zobaczyć. Nie muszą być bardzo skomplikowane, ale też żeby było to coś lepszego od Hello World. Dla aplikacji z bazą danych zaproponuj taką dostępną aplikację, która ma bazę wypełnioną przykładowymi danymi, żeby można było je w przyszłości przeglądać, edytować, a także przeglądać logi bazodanowe.
Dla zainstalowania tych aplikacji musi powstać kod w GitHubie, który utworzy kapsuły w systemie ReplicaSet albo Deployment (doradź najlepszy sposób, żeby te aplikacje były uruchamiane na wszystkich węzłach klastra).
Utworzenie co najmniej dwóch grup użytkowników – admin, viewer i utworzenie po jednym użytkowniku dla każdej grupy. Przypisanie odpowiednich ról, żeby można było testować uprawnienia.

Etap 3:
Instalacja systemu Octant, żeby podglądać stan klastra w interfejsie graficznym

Etap 4:
wdrożenie systemu Grafana lub elasticsearch przy pomocy daemonset, żeby zbierać logi i być w stanie je oglądać
Etap 5:
Wdrożenie Jenkinsa, żeby nauczyć się tego systemu i zautomatyzować wdrożenie nowej wersji aplikacji. Można to zrobić na przykładzie nowej aplikacji wykonującej jakąś prostą czynność, która następnie będzie rozbudowywana o kolejne funkcjonalności. Np. podanie adresu IP i nazwy hosta (kapsuły). Potem dodanie godziny, potem czegoś jeszcze, itd.
