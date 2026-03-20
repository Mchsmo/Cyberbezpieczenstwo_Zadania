# Instrukcja tworzenia bezpiecznego woluminu VeraCrypt i weryfikacji instalatora

Sprawozdanie opisujące proces bezpiecznej instalacji oprogramowania VeraCrypt oraz kroki do stworzenia zaszyfrowanego kontenera na dane.

## 1\. Weryfikacja autentyczności instalatora

Zanim przystąpimy do instalacji, kluczowe jest upewnienie się, że pobrany plik nie został zmodyfikowany przez osoby trzecie. Poprzez sprawdzeni sumy kontrolnej i weryfikacje podpisu PGP.

### Sprawdzenie sumy kontrolnej SHA-256

Wykorzystałem narzędzie systemowe `certutil` do obliczenia skrótu pliku instalacyjnego i porównania go z oficjalną listą sum kontrolnych.

**Wynik:** 
![alt text](https://github.com/Mchsmo/Cyberbezpieczenstwo_Zadania/blob/zad-vera_crypt/images/img0.png)

### Weryfikacja podpisu cyfrowego (OpenPGP)

Przy użyciu programu **Kleopatra** dokonałem weryfikacji podpisu pliku `.sig`. Potwierdza to integralność instalatora.

**Wynik:**

![alt text](https://github.com/Mchsmo/Cyberbezpieczenstwo_Zadania/blob/zad-vera_crypt/images/img1.png)

-----

## 2\. Tworzenie zaszyfrowanego woluminu

Najważniejsze kroki przy tworzeniu kontenera w programie **VeraCrypt**.

### - Określenie jaki rodzaj woluminu chcemy utworzyć:

można wybrać pomiędzy stworzeniem zaszyfrowanego *pliku*, zaszyfrowaniem całej *partycji*, lub nawet całego *systemu*.

### - Wybranie miejsca w którym chcemy utworzyć plik

Program pozwala na wykorzystanie systemowego eksploratora plików, żeby zdecydować o miejscu położenia pliku. (Przy nadawaniu sugerowane jest rozszerzenie `.hc`, ale plik będzie działał po nadaniu dowolnego rozszerzenia, chociaż np: rozszerzenie `.png`, lub inne rozszerzenie typu *"executable"* może spowodować pojawieniem się ostrzeżenia o tym, że system i antywirus mogą ingerować w działanie woluminu)
 
### - Określenie rozmiaru

Wybieramy rozmiar woluminu.
 
![alt text](https://github.com/Mchsmo/Cyberbezpieczenstwo_Zadania/blob/zad-vera_crypt/images/img2.png)

### - Ustawienie zabezpieczeń (Hasło i Keyfiles)

Dla zapewnienia wyższego poziomu bezpieczeństwa użyłem klucza i keyfile.

1.  **Silne hasło (Pass Phrase):** Skomplikowany ciąg znaków.
2.  **Keyfiles (Pliki klucze):** Zaznaczyłem opcję `Use keyfiles`. Dostęp do danych wymaga fizycznego posiadania określonego pliku, który powinien być przechowywany na pendrive razem z woluminem.

![alt text](https://github.com/Mchsmo/Cyberbezpieczenstwo_Zadania/blob/zad-vera_crypt/images/img3.png)

-----

## 3\. Montowanie i zarządzanie zasobami

Po utworzeniu woluminu (plik `VeraCrypt.hc`), można go zamontować w systemie jako wirtualny dysk.

1.  W oknie głównym wybieramy literę napędu (np. **A:**).
2.  Po poprawnym podaniu hasła i wskazaniu plików-kluczy, wolumin staje się dostępny w eksploratorze plików.
3.  Wewnątrz woluminu można bezpiecznie przechowywać wrażliwe dane, takie jak kopie certyfikatów (`.asc`) czy pliki unieważnienia (`.rev`).

![alt text](https://github.com/Mchsmo/Cyberbezpieczenstwo_Zadania/blob/zad-vera_crypt/images/img4.png)
