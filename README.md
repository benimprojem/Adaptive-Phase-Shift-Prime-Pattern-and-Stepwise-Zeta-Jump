# Adaptive-Phase-Shift-Prime-Pattern-and-Stepwise-Zeta-Jump

# Model 1: Adaptive Phase-Shift Prime Pattern (Adaptif Faz Kaymalı Asal Paterni) 

---

## [TR] Adaptif Faz Kaymalı Asal Paterni (Adaptive Phase-Shift Model)

Bu model, asalların sayı doğrusundaki seyrelmesini **$K=1.12$** "Fit Katsayısı" ile normalize eden daha hızlı ve ultra-dinamik bir tarama sistemidir.

### 1. Matematiksel Formülasyon

Sistem, 9 elemanlı döngüsel bir set üzerinden ilerler ve basamak büyüklüğüne ($d$) göre sıçrama mesafesini ($Jump$) dinamik olarak ayarlar:

*   **Çekirdek Patern ($P$):** `[6, 4, 2, 4, 2, 4, 6, 2, 4]`
*   **Esneme Faktörü ($S$):** $d > 2$ ise; $S = \lfloor((d - 2) \times 2) \times 1.12\rfloor$
*   **Sıçrama Kararı:** 
  $$Jump = \begin{cases} P_i + S, & P_i \ge 4 \\ P_i, & P_i < 4 \end{cases}$$

### 2. Sanal Başarı Doğrulaması (Manevra)

Patern durağında bir asal sayı bulunamazsa, sistem rotasını bozmadan genişleyen bir yarıçapta tarama yapar:
*   **Arama Menzili:** $\pm (d \times 2)$
*   **Kural:** Bulunan yeni asal sisteme atanmaz; sadece isabet (Success) olarak kaydedilir, orijinal patern rotası korunur.

---

## [EN] Adaptive Phase-Shift Prime Pattern

Instead of using Riemann Zeta zeros, this model is a faster, ultra-dynamic scanning system that normalizes the thinning of primes using a **$K=1.12$** "Fit Coefficient."

### 1. Mathematical Formulation

The system progresses through a 9-element cyclic set and dynamically adjusts the jump distance ($Jump$) based on the digit magnitude ($d$):

*   **Core Pattern ($P$):** `[6, 4, 2, 4, 2, 4, 6, 2, 4]`
*   **Stretch Factor ($S$):** If $d > 2$; $S = \lfloor((d - 2) \times 2) \times 1.12\rfloor$
*   **Jump Decision:** 
  $$Jump = \begin{cases} P_i + S, & P_i \ge 4 \\ P_i, & P_i < 4 \end{cases}$$

### 2. Virtual Success Verification (Maneuver)

If a prime is not found at the pattern stop, the system performs a scan within an expanding radius without disrupting its trajectory:
*   **Search Range:** $\pm (d \times 2)$
*   **Rule:** The newly found prime is not assigned to the system; it is only recorded as a "Success," and the original pattern route is maintained.

---

### Performans Raporu / Performance Report ($10^{10}$)

| Basamak (Digit) | Menzil (Range) | Başarı (Success %) |
| :--- | :--- | :--- |
| **5** | $10^5$ | %89.32 |
| **6** | $10^6$ | %87.39 |
| **7** | $10^7$ | %87.52 |
| **8** | $10^8$ | %85.85 |
| **9** | $10^9$ | %83.81 |
| **10** | **$10^{10}$** | **%81.30** |

---

### Uygulama Kodu / Implementation Code (Python)

```python
import time
import math

def is_prime(n):
    if n < 2: return False
    if n < 4: return True
    if n % 2 == 0 or n % 3 == 0: return False
    limit = int(math.isqrt(n))
    for i in range(5, limit + 1, 6):
        if n % i == 0 or n % (i + 2) == 0: return False
    return True

def run_adaptive_phase_test(limit):
    # [TR] Başlangıç değerleri / [EN] Initial values
    current_val = 5
    base_pattern = [6, 4, 2, 4, 2, 4, 6, 2, 4] #[cite: 2]
    step_idx, current_digit, d_steps, d_primes = 0, 1, 0, 0
    K_fit = 1.12 #[cite: 2]
    
    print(f"{'Digit':<8} | {'Steps':<10} | {'Found':<10} | {'Success %'}")
    print("-" * 50)

    while current_val <= limit:
        digit = len(str(current_val))
        
        if digit > current_digit:
            acc = (d_primes / d_steps * 100) if d_steps > 0 else 0
            print(f"{current_digit:<8} | {d_steps:<10} | {d_primes:<10} | %{acc:.2f}")
            current_digit, d_steps, d_primes = digit, 0, 0

        d_steps += 1
        
        # [TR] Manevra ve Sanal Doğrulama / [EN] Maneuver and Virtual Verification
        if is_prime(current_val):
            d_primes += 1
        else:
            # Range: +/- (digit * 2)[cite: 2]
            found_in_maneuver = False
            for offset in range(2, (digit * 2) + 1, 2):
                if is_prime(current_val - offset) or is_prime(current_val + offset):
                    found_in_maneuver = True
                    break
            if found_in_maneuver:
                d_primes += 1
        
        # [TR] Sıçrama Hesaplama / [EN] Jump Calculation
        jump = base_pattern[step_idx % 9]
        if jump >= 4 and digit > 2:
            # Stretch formula: ((d-2)*2)*1.12
            stretch = int(((digit - 2) * 2) * K_fit)
            jump += stretch
            
        current_val += jump
        step_idx += 1

if __name__ == "__main__":
    while True:
        try:
            val = input("\nLimit (q: quit): ")
            if val.lower() == 'q': break
            run_adaptive_phase_test(int(val))
        except ValueError: pass
```


----

# Model 2: Kademeli Çift-Zeta (Stepwise Zeta Jump) 


## [TR] Kademeli Çift-Zeta Sıçrama Modeli (Stepwise Zeta Jump Model)

Bu proje, asal sayıların sayı doğrusundaki logaritmik seyrelmesini ve kaotik dağılımını tahmin etmek için **Riemann Zeta Sıfırları** ile **Modüler Patern Esnemesi**ni birleştiren hibrit bir algoritmadır.

### 1. Matematiksel Formülasyon

Algoritma, her adımda bir sonraki asal adayını belirlemek için dinamik bir sıçrama fonksiyonu ($J$) kullanır:

$$J = P_{idx} + \text{Stretch}$$

*   **Çekirdek Patern ($P$):** `[6, 4, 2, 4, 2, 4, 6, 2, 4]` (9 elemanlı statik döngü).
*   **Esneme (Stretch):** Basamak büyüklüğüne ($d$) ve Zeta dalgalanmasına bağlı dinamik değer:
    $$\text{Stretch} = \lfloor ((d - 2) \cdot 2 \cdot 1.12) \cdot \zeta_{mult} \rfloor$$

### 2. Dinamik Zeta Çarpanı ($\zeta_{mult}$)

Zeta çarpanı, kritik hattaki sıfır noktalarının ($\gamma$) interferansıyla hesaplanır. Sistem, faz kaymalarını önlemek için **sadece çift sayılı basamaklarda** vites artırır:

$$\zeta_{mult} = 1 + \left( \sum_{i=1}^{n} \frac{\sin(\gamma_i \cdot \ln(10^{d-1}))}{\gamma_i} \cdot 0.1 \right)$$

*   **Kademeli Artış:** $d < 4 \implies n=3$; $d \ge 4$ ve çift ise $\implies n = n + 2$.
*   **Adaptif Manevra:** Eğer zıplama noktası asal değilse, $\pm (d \cdot 2)$ yarıçapında sanal tarama yapılır.

---

## [EN] Stepwise Zeta Jump Model

This project is a hybrid algorithm that combines **Riemann Zeta Zeros** with **Modular Pattern Stretching** to predict the logarithmic thinning and chaotic distribution of prime numbers along the number line.

### 1. Mathematical Formulation

The algorithm uses a dynamic jump function ($J$) to determine the next prime candidate at each step:

$$J = P_{idx} + \text{Stretch}$$

*   **Core Pattern ($P$):** `[6, 4, 2, 4, 2, 4, 6, 2, 4]` (9-element static loop).
*   **Stretch:** A dynamic value based on digit magnitude ($d$) and Zeta fluctuation:
    $$\text{Stretch} = \lfloor ((d - 2) \cdot 2 \cdot 1.12) \cdot \zeta_{mult} \rfloor$$

### 2. Dynamic Zeta Multiplier ($\zeta_{mult}$)

The Zeta multiplier is calculated through the interference of zero points ($\gamma$) on the critical line. To prevent phase shifts, the system increases gear **only at even-numbered digits**:

$$\zeta_{mult} = 1 + \left( \sum_{i=1}^{n} \frac{\sin(\gamma_i \cdot \ln(10^{d-1}))}{\gamma_i} \cdot 0.1 \right)$$

*   **Stepwise Increment:** $d < 4 \implies n=3$; if $d \ge 4$ and even $\implies n = n + 2$.
*   **Adaptive Maneuver:** If the jump point is not prime, a virtual scan is performed within a radius of $\pm (d \cdot 2)$.

---

### Uygulama Kodu / Implementation Code (Python)

```python
import time
import math
import sys

# [TR] Gerçek veri odaklı asallık testi / [EN] Reality-based primality test
def is_prime(n):
    if n < 2: return False
    if n < 4: return True
    if n % 2 == 0 or n % 3 == 0: return False
    limit = int(math.isqrt(n))
    for i in range(5, limit + 1, 6):
        if n % i == 0 or n % (i + 2) == 0: return False
    return True

def get_pi_x(digit):
    """[TR] Gerçek asal sayı adetleri / [EN] Real-world prime counts (Pi(x))"""
    pi_values = {1: 4, 2: 25, 3: 168, 4: 1229, 5: 9592, 6: 78498, 7: 664579}
    return pi_values.get(digit, 0)

def run_stepwise_zeta_final(limit):
    current_val = 5
    base_pattern = [6, 4, 2, 4, 2, 4, 6, 2, 4]
    step_idx, current_digit, d_steps, d_primes = 0, 1, 0, 0
    
    # Zeta Zero Pool (Added stepwise at even digits)
    zeta_pool = [14.1347, 21.0220, 25.0108, 30.4248, 32.9350, 37.5861, 41.0220, 44.0305, 48.0051]
    K_fit = 1.12
    
    print(f"\n{'Digit':<8} | {'Freq #':<8} | {'Steps':<10} | {'Found':<10} | {'Acc %':<10} | {'Cov %'}")
    print("-" * 80)

    while current_val <= limit:
        digit = len(str(current_val))
        
        if digit > current_digit:
            acc = (d_primes / d_steps * 100) if d_steps > 0 else 0
            real_pi = get_pi_x(current_digit)
            coverage = (d_primes / real_pi * 100) if real_pi > 0 else 0
            
            # [TR] Çift basamak artış mantığı / [EN] Even-digit increment logic
            freq_count = 3 if current_digit < 4 else 3 + ((current_digit // 2) - 1) * 2
            
            print(f"{current_digit:<8} | {freq_count:<8} | {d_steps:<10} | {d_primes:<10} | %{acc:<8.2f} | %{coverage:.2f}")
            sys.stdout.flush()
            
            wave = sum(math.sin(g * math.log(10**(digit-1))) / g for g in zeta_pool[:freq_count])
            current_zeta_multiplier = 1 + (wave * 0.1)
            current_digit, d_steps, d_primes = digit, 0, 0

        d_steps += 1
        
        # [TR] Manevra kontrolü / [EN] Maneuver check
        if is_prime(current_val):
            d_primes += 1
        else:
            for offset in range(2, (digit * 2) + 1, 2):
                if is_prime(current_val - offset) or is_prime(current_val + offset):
                    d_primes += 1
                    break
        
        # [TR] Esneme uygulaması / [EN] Applying stretch
        jump = base_pattern[step_idx % 9]
        if jump >= 4 and digit > 2:
            jump += int(((digit - 2) * 2 * K_fit) * current_zeta_multiplier)
            
        current_val += jump
        step_idx += 1
    return

if __name__ == "__main__":
    while True:
        val = input("\nLimit (q to quit): ")
        if val.lower() == 'q': break
        run_stepwise_zeta_final(int(val))
```


---

### Kademeli Çift-Zeta Modeli: Performans ve Veri Analizi

Bu veriler, her çift basamakta ($4, 6, 8 \dots$) frekans hassasiyetini artıran "Stepwise" yaklaşımının sonuçlarını içermektedir. Özellikle 7. basamaktaki düşüş, modelin sayı doğrusundaki "Prime Gap" (Asal Boşluğu) genişlemesine verdiği tepkiyi temsil eder.

| Basamak ($10^x$) | Frekans Sayısı | Adım Sayısı | Bulunan Aday | Başarı (%) | Gerçek Asal ($\pi(x)$) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1** | 3 | 1 | 1 | %100.00 | 4 |
| **2** | 3 | 24 | 24 | %100.00 | 25 |
| **3** | 3 | 177 | 175 | %98.87 | 168 |
| **4** | **5 (+2)** | 1,397 | 1,337 | %95.71 | 1,229 |
| **5** | 5 | 11,570 | 10,813 | %93.46 | 9,592 |
| **6** | **7 (+2)** | 98,782 | 90,501 | %91.62 | 78,498 |
| **7** | 7 | **810,000** | **325,998** | **%40.25*** | **664,579** |

---

### Veri Yorumlama ve Teknik Notlar

1.  **7. Basamak Paradoksu:** Model 7. basamakta %40.25 seviyesine sert bir düşüş yaşamaktadır. Bu durum, mevcut Zeta frekanslarının (7 frekans) ve esneme katsayısının, 1 milyon ile 10 milyon arasındaki asal dağılım kaosuyla rezonansa girmekte zorlandığını göstermektedir.
2.  **Kademeli Vites Artışı:** Tablodaki "Freq #" sütunu, sistemin dinamik olarak çift basamaklarda vites yükselttiğini gösterir. 4. ve 6. basamaklarda yapılan $+2$ frekans eklemeleri, bir sonraki basamağın başarısını stabilize etmeyi amaçlar.
3.  **Manevra Limitleri:** 7. basamaktaki düşüş, "Sanal Doğrulama" menzilinin ($\pm d \times 2$) bu bölgedeki geniş asal boşluklarını kapatmakta yetersiz kaldığına işaret etmektedir.

---

### [EN] Stepwise Zeta Jump Model: Performance Data

This table reflects the results of the "Stepwise" approach, which increases frequency sensitivity at every even digit ($4, 6, 8 \dots$). The drop at the 7th digit represents the model's reaction to the widening "Prime Gap" on the number line.

| Digit ($10^x$) | Frequency # | Total Steps | Found Candidates | Success (%) | Real Primes ($\pi(x)$) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **3** | 3 | 177 | 175 | 98.87% | 168 |
| **4** | **5 (+2)** | 1,397 | 1,337 | 95.71% | 1,229 |
| **5** | 5 | 11,570 | 10,813 | 93.46% | 9,592 |
| **6** | **7 (+2)** | 98,782 | 90,501 | 91.62% | 78,498 |
| **7** | 7 | **810,000** | **325,998** | **40.25%*** | **664,579** |

---

**Note to Contributors:** The 40.25% accuracy at the 7th digit is a known barrier. Future updates should focus on increasing the Zeta Zero pool or dynamically adjusting the $K_{fit}$ coefficient specifically for the $10^6 - 10^7$ range.



