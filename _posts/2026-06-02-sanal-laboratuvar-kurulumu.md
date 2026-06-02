---
title: "Sanal Laboratuvar (Home Lab) Kurulumu: Güvenli Sızma Testi Ortamı Oluşturma"
date: 2026-06-02 17:35:00 +0300
categories: [Siber Güvenlik, Laboratuvar]
tags: [Kali-Linux, Parrot-OS, VirtualBox, Nmap, Metasploit, Home-Lab]
---

Herkese merhaba, blogumun bu ilk yazısında siber güvenlik çalışmalarının kalbi olan "Sanal Laboratuvar" (Home Lab) kurulumundan bahsedeceğim.

Sistemlerin arka planında yatan mekanizmaları anlamanın ve zafiyetleri tespit etmenin en iyi yolu, teoriyi pratiğe dökmektir. Ancak bu testleri gerçek ve canlı sistemler üzerinde yapmak yasal ve etik sorunlar doğurur. Bu nedenle sızma testi (PenTest) senaryolarımızı tamamen izole edilmiş, güvenli bir ev laboratuvarında gerçekleştirmemiz gerekir.

### Neden Kendi Laboratuvarımızı Kurmalıyız?
Dışarıdan sağlanan platformlar pratik yapmak için iyi olsa da, kendi ağ mimarini sıfırdan inşa etmek sistemin tüm katmanlarına hakim olmanı sağlar. Hedef makineleri kendi kurallarına göre yapılandırmak, ağ trafiğini içeriden izlemek ve sömürü (exploit) sonrası adımları özgürce test etmek paha biçilemez bir deneyimdir.

### Donanım ve Hipervizör Seçimi
Birden fazla sanal makineyi (VM) aynı anda çalıştırmak ciddi bir sistem kaynağı gerektirir. Kendi laboratuvarımı i7-14650HX işlemcili ve RTX 5060 grafik kartına sahip sistemim üzerinde yapılandırdım. Bu yüksek çekirdek gücü ve kapasite, saldırgan ve hedef makineleri herhangi bir performans darboğazı yaşamadan, akıcı bir şekilde eşzamanlı olarak ayağa kaldırmama olanak tanıyor.

Sanallaştırma altyapısı (Hipervizör) olarak **VirtualBox** kullanıyorum. Açık kaynaklı olması ve ağ izolasyonu konusundaki esnek yetenekleri, laboratuvar ortamı için onu ideal kılıyor.

### Saldırgan Makinelerin Hazırlanması: Kali ve Parrot OS
Laboratuvarın ofansif tarafında, siber güvenlik dünyasının endüstri standartları olan iki farklı dağıtımı aktif olarak kullanıyorum:
1. **Kali Linux:** Geniş araç yelpazesiyle ana sızma testi platformum.
2. **Parrot OS:** Daha hafif yapısı ve geliştirici dostu arayüzü sayesinde alternatif test senaryolarında başvurduğum ana sistemlerden biri.

### Ağ İzolasyonu (NAT Network)
Güvenliğin en kritik noktası burasıdır. Sanal makinelerin bizim ev ağımıza (Host) veya internete kontrolsüzce erişmesini istemeyiz. VirtualBox üzerinden özel bir **NAT Network** (Örn: `10.0.2.0/24`) oluşturarak, saldırgan ve hedef makineleri sadece birbirleriyle konuşabilecekleri kapalı bir odaya alıyoruz.

### İlk Testler ve Keşif Aşaması
Laboratuvar kurulduktan sonraki ilk adım, hedefleri tanımaktır. Ağ üzerindeki aktif cihazları ve açık portları tespit etmek için **Nmap** ile ilk taramalarımızı gerçekleştiririz. Örnek bir versiyon tespiti taraması:
```bash
nmap -sV -O 10.0.2.15
