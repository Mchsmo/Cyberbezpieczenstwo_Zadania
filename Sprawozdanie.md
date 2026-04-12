
# Sprawozdanie: Konfiguracja Środowiska Wirtualnego i Sieciowego

---

## 1. Weryfikacja obrazów ISO
Przed rozpoczęciem instalacji sprawdzono integralność pobranych plików ISO za pomocą sum kontrolnych SHA256, aby upewnić się, że obrazy nie zostały zmodyfikowane.

**Zrzut ekranu: Weryfikacja sum kontrolnych (CertUtil)**
![Weryfikacja ISO](https://raw.githubusercontent.com/Mchsmo/Cyberbezpieczenstwo_Zadania/Zad_VM/images/img0.png)

---

## 2. Optymalizacja i Migawki (Snapshots)
W systemach skonfigurowano dwukierunkowy schowek oraz funkcję przeciągania plików, co znacznie ułatwia pracę między hostem a maszynami wirtualnymi.

**Zrzut ekranu: Ustawienia schowka i Drag-and-Drop**
![Integracja](https://raw.githubusercontent.com/Mchsmo/Cyberbezpieczenstwo_Zadania/Zad_VM/images/img4.png)

Zaraz po czystej instalacji i konfiguracji dodatków wykonano bazową migawkę systemu, umożliwiającą natychmiastowy powrót do punktu wyjścia.

**Zrzut ekranu: Menedżer migawek (Snapshoty)**
![Migawki](https://raw.githubusercontent.com/Mchsmo/Cyberbezpieczenstwo_Zadania/Zad_VM/images/img5.png)

---
### 3 Konfiguracja Foldera Współdzielonego (Dokumentacja)
Zgodnie z wymaganiami instrukcji, skonfigurowano folder współdzielony, który pozwala na łatwą wymianę plików między hostem (systemem Windows) a obiema maszynami wirtualnymi.

Stworzono folder na pulpicie hosta: `C:\Users\Michał\Desktop\VM_Share`.
W ustawieniach VirtualBox dla obu maszyn dodano ten folder z opcją Pełny dostęp i **Automatyczne montowanie**.

**Dokumentacja konfiguracji foldera współdzielonego na systemie Kali Linux:**
![Konfiguracja Share w Kali](https://raw.githubusercontent.com/Mchsmo/Cyberbezpieczenstwo_Zadania/Zad_VM/images/img11.png)
*Screenshot 1: Widok ustawień foldera współdzielonego dla maszyny Kali Linux.*

**Dokumentacja konfiguracji foldera współdzielonego na systemie Ubuntu:**
![Konfiguracja Share w Ubuntu](https://raw.githubusercontent.com/Mchsmo/Cyberbezpieczenstwo_Zadania/Zad_VM/images/img12.png)
*Screenshot 2: Widok ustawień foldera współdzielonego dla maszyny Ubuntu.*

---

## 4. Konfiguracja Sieciowa
### 4.1. Problem z domyślnym trybem NAT
Początkowo maszyny pracowały w trybie NAT (img1). Zaobserwowano, że obie maszyny otrzymały ten sam adres IP `10.0.2.15`, co uniemożliwiło komunikację między nimi mimo pozytywnego wyniku testu ping (pingowano interfejs zwrotny).

**Zrzut ekranu: Konflikt adresów IP w trybie NAT**
![Adres IP Ubuntu](https://raw.githubusercontent.com/Mchsmo/Cyberbezpieczenstwo_Zadania/Zad_VM/images/img2.png)
![Adres IP Kali](https://raw.githubusercontent.com/Mchsmo/Cyberbezpieczenstwo_Zadania/Zad_VM/images/img3.png)

### 4.2. Rozwiązanie: Sieć NAT (NAT Network)
Zmieniono konfigurację na "Sieć NAT" o nazwie `NatNetwork`. Pozwoliło to na przydzielenie unikalnych adresów IP w jednej podsieci wirtualnej.

**Zrzut ekranu: Poprawna konfiguracja sieci**
![NAT Network Settings](https://raw.githubusercontent.com/Mchsmo/Cyberbezpieczenstwo_Zadania/Zad_VM/images/img8.png)

---

## 5. Usługi Sieciowe i Bezpieczeństwo
### 5.1. Serwer SSH i Firewall
Zgodnie z poleceniem, skonfigurowano połączenie SSH między maszynami. Ponieważ SSH jest usługą sieciową, konieczne było również skonfigurowanie zapory sieciowej (ścian ogniowych).

1.  **Na systemie Ubuntu (Serwer):**
    ```bash
    # Instalacja serwera SSH
    sudo apt update
    sudo apt install openssh-server

    # Uruchomienie usługi
    sudo systemctl enable --now ssh

    # Konfiguracja Firewalla (UFW)
    sudo ufw allow ssh
    sudo ufw enable
    ```
2.  **Ściana ogniowa (UFW):** Zweryfikowano status firewalla na Ubuntu, potwierdzając, że port 22 (SSH) jest otwarty dla ruchu.

**Zrzut ekranu: Konfiguracja firewall i aktywacja SSH**
![UFW i SSH](https://raw.githubusercontent.com/Mchsmo/Cyberbezpieczenstwo_Zadania/Zad_VM/images/img6.png)
![Status SSH](https://raw.githubusercontent.com/Mchsmo/Cyberbezpieczenstwo_Zadania/Zad_VM/images/img7.png)

### 5.2. Logowanie kluczem publicznym
W celu wyeliminowania konieczności wpisywania hasła i zwiększenia bezpieczeństwa, wygenerowano parę kluczy na Kali Linux i przesłano klucz publiczny do Ubuntu za pomocą `ssh-copy-id`.


**Zrzut ekranu: Przesyłanie klucza SSH na Ubuntu**
![SSH Copy ID](https://raw.githubusercontent.com/Mchsmo/Cyberbezpieczenstwo_Zadania/Zad_VM/images/img10.png)

---