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
Serwery zainstalowane. Skonfigurowany stały adres IP, logowanie po kluczu ssh.
Sudo apt install docker.io -y
Sudo systemctl enable docker
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install kubeadm kubelet kubectl
sudo apt-mark hold kubeadm kubelet kubectl – zatrzymanie automatycznej aktualizacji pakietów
sudo swapoff -a – blokuje swap spaces
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
sudo vim /etc/modules-load.d/containerd.conf
W pliku wpisać dwie linie: 
Overlay
br_netfilter

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

Tyle na początek. Zadanie jest dla mnie bardzo skomplikowane. Nie pisz mi od razu jak je całe zrobić. Sam podzieliłem je na etapy. Przeanalizuj cały tekst i na początek zaproponuj tylko parametry dla maszyn wirtualnych. Ja potem wykonam na tej podstawie etap 1, a potem dopiero przejdziemy do etapu 2 i wtedy poproszę o kolejne wskazówki. Jeśli w etapie 1 znajdujesz błędy w rozumowaniu, albo są jakieś dobre praktyki, które można wdrożyć na tym etapie to też mnie o tym poinformuj.
