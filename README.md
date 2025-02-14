<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hızlı Tıkla Oyunu</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin-top: 50px;
            position: relative;
        }
        #kutu, #bomba {
            width: 100px;
            height: 100px;
            position: absolute;
            display: none;
            cursor: pointer;
        }
        #kutu {
            background-color: red;
        }
        #bomba {
            background-color: black;
        }
        #skor {
            font-size: 24px;
            margin-bottom: 20px;
            display: none;
        }
        #anaMenu {
            margin-bottom: 20px;
        }
        #enYuksekSkor {
            font-size: 18px;
            display: none;
        }
        #sure {
            font-size: 18px;
            display: none;
        }
        #can {
            font-size: 18px;
            display: none;
        }
        .modButon {
            padding: 10px 20px;
            font-size: 18px;
            cursor: pointer;
            margin: 5px;
        }
    </style>
</head>
<body>
    <h1>Hızlı Tıkla Oyunu</h1>
    <div id="enYuksekSkor">En Yüksek Skor: 0</div>
    <div id="sure">Süre: 0</div>
    <div id="can">Can: 3</div>
    <div id="anaMenu">
        <button class="modButon" data-sure="10">10 Saniye Modu</button>
        <button class="modButon" data-sure="20">20 Saniye Modu</button>
        <button class="modButon" data-sure="30">30 Saniye Modu</button>
        <button class="modButon" data-sure="40">40 Saniye Modu</button>
    </div>
    <div id="skor">Skor: 0</div>
    <div id="kutu"></div>
    <div id="bomba"></div>

    <script>
        let skor = 0;
        let can = 3;
        let kutu = document.getElementById("kutu");
        let bomba = document.getElementById("bomba");
        let skorElement = document.getElementById("skor");
        let enYuksekSkorElement = document.getElementById("enYuksekSkor");
        let sureElement = document.getElementById("sure");
        let canElement = document.getElementById("can");
        let anaMenu = document.getElementById("anaMenu");
        let modButonlari = document.querySelectorAll(".modButon");
        let enYuksekSkorlar = {10: 0, 20: 0, 30: 0, 40: 0};
        let oyunSuresi = 0;
        let geriSayim;

        // Kutuya tıklandığında skoru artır ve yeni pozisyon ver
        kutu.addEventListener("click", function() {
            skor++;
            skorElement.innerText = "Skor: " + skor;
            kutu.style.display = "none";
            hareketliKutu();
            hareketliBomba(); // Bombayı da yeniden konumlandır
        });

        // Bombaya tıklandığında can azalt
        bomba.addEventListener("click", function() {
            can--;
            canElement.innerText = "Can: " + can;
            if (can <= 0) {
                oyunBitti("Canların bitti! Oyun bitti.");
            } else {
                hareketliBomba(); // Bombayı yeniden konumlandır
            }
        });

        // Mod butonlarına tıklandığında oyunu başlat
        modButonlari.forEach(function(buton) {
            buton.addEventListener("click", function() {
                oyunSuresi = parseInt(buton.getAttribute("data-sure"));
                baslaOyun(oyunSuresi);
            });
        });

        // Oyunu başlat
        function baslaOyun(sure) {
            skor = 0;
            can = 3;
            skorElement.innerText = "Skor: 0";
            canElement.innerText = "Can: 3";
            sureElement.innerText = "Süre: " + sure;
            anaMenu.style.display = "none";
            skorElement.style.display = "block";
            sureElement.style.display = "block";
            canElement.style.display = "block";
            enYuksekSkorElement.style.display = "block";
            enYuksekSkorElement.innerText = "En Yüksek Skor: " + enYuksekSkorlar[oyunSuresi];
            kutu.style.display = "block";
            bomba.style.display = "block";
            hareketliKutu();
            hareketliBomba();

            // Geri sayım başlat
            clearInterval(geriSayim);
            geriSayim = setInterval(function() {
                sure--;
                sureElement.innerText = "Süre: " + sure;
                if (sure <= 0) {
                    clearInterval(geriSayim);
                    oyunBitti("Süre doldu! Oyun bitti.");
                }
            }, 1000);
        }

        // Kutuyu rastgele bir pozisyona yerleştir
        function hareketliKutu() {
            let x, y;
            do {
                x = Math.random() * (window.innerWidth - 100);
                y = Math.random() * (window.innerHeight - 100);
            } while (cakismaVar(x, y, bomba)); // Bombayla çakışma kontrolü
            kutu.style.left = x + "px";
            kutu.style.top = y + "px";
            kutu.style.display = "block";
        }

        // Bombayı rastgele bir pozisyona yerleştir
        function hareketliBomba() {
            let x, y;
            do {
                x = Math.random() * (window.innerWidth - 100);
                y = Math.random() * (window.innerHeight - 100);
            } while (cakismaVar(x, y, kutu)); // Kutuyu çakışma kontrolü
            bomba.style.left = x + "px";
            bomba.style.top = y + "px";
            bomba.style.display = "block";
        }

        // Çakışma kontrolü
        function cakismaVar(x, y, element) {
            let elemX = parseFloat(element.style.left);
            let elemY = parseFloat(element.style.top);
            return Math.abs(x - elemX) < 100 && Math.abs(y - elemY) < 100;
        }

        // Oyun bittiğinde yapılacak işlemler
        function oyunBitti(mesaj) {
            kutu.style.display = "none";
            bomba.style.display = "none";
            skorElement.style.display = "none";
            sureElement.style.display = "none";
            canElement.style.display = "none";
            enYuksekSkorElement.style.display = "none";
            anaMenu.style.display = "block";
            alert(mesaj + " Skorunuz: " + skor);

            // En yüksek skoru güncelle
            if (skor > enYuksekSkorlar[oyunSuresi]) {
                enYuksekSkorlar[oyunSuresi] = skor;
                enYuksekSkorElement.innerText = "En Yüksek Skor: " + enYuksekSkorlar[oyunSuresi];
            }
        }
    </script>
</body>
</html>
