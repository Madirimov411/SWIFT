# 📱 ViewController Lifecycle (Swift - iOS)

Bu hujjatda iOS ilovalarida `UIViewController` ning hayotiy sikli (Lifecycle) qanday ishlashi haqida to‘liq ma’lumot beriladi. 🍎  
ViewController bu — har bir ekran uchun alohida ob’yekt. Uning **lifecycle** metodlari orqali siz ilovaning turli bosqichlarida kerakli kodni ishlatishingiz mumkin.

---

## 📌 ViewController nima va qanday “yashaydi”?

`UIViewController` — iOS ilovasidagi har bir ekran (yoki sahifa) uchun asosiy komponent.  
Tizim (iOS) quyidagi tartibda ViewController’ni yaratadi va boshqaradi — bu **lifecycle** deb nomlanadi.

---

## 🚦 Lifecycle Bosqichlari

### ✅ 1. `viewDidLoad()`

- ViewController birinchi marta yaratilganda chaqiriladi.
- IBOutlets (label, button, tableView va h.k.) bu vaqtda mavjud bo‘ladi.
- Odatda bu yerda UI sozlashlar, delegate berishlar, boshlang‘ich holatlar tayinlanadi.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    print("viewDidLoad called")
}
```

## ✅ 2. `viewWillAppear(_:)`

*   ViewController ekranda **ko‘rinishidan oldin** chaqiriladi.
*   Bu yerda siz:
    - `navigationBar` ni yashirishingiz yoki ko‘rsatishingiz,
    - UI holatini dinamik o‘zgartirishingiz mumkin.

```swift
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    print("viewWillAppear called")
}
```

## ✅ 3. `viewDidAppear(_:)`

- View to‘liq ekranda **ko‘ringanidan keyin** chaqiriladi.
- Bu joyda siz:
  - Animatsiyalarni boshlashingiz,
  - Timer’larni ishga tushirishingiz,
  - Foydalanuvchiga darhol ko‘rinadigan funksiyalarni ishga tushirishingiz mumkin.

```swift
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    print("viewDidAppear called")
}
```

## ✅ 4. `viewWillDisappear(_:)`

- ViewController boshqa sahifaga o‘tayotganida yoki yopilayotganida chaqiriladi.
- Bu metod foydalanuvchiga hali ko‘rinib turgan, ammo tez orada yo‘qoladigan UI holatlarini tayyorlash uchun ishlatiladi.

📌 Misol:
- Video/audio’ni pauza qilish
- API so‘rovlarini to‘xtatish
- UI elementlarni yashirish

```swift
override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)
    print("viewWillDisappear called")
}
```

## ✅ 5. `viewDidDisappear(_:)`

- View butunlay ekrandan **yo‘qolgach** chaqiriladi.
- Bu metodda siz:
  - Resurslarni tozalashingiz,
  - Loglar yozishingiz,
  - Timer’larni o‘chirishingiz mumkin.

```swift
override func viewDidDisappear(_ animated: Bool) {
    super.viewDidDisappear(animated)
    print("viewDidDisappear called")
}
```

## 🤔 `prepare(for:sender:)` qanday ishlaydi?

Agar siz storyboard’da `segue` orqali boshqa ViewController’ga o‘tsangiz, bu metod chaqiriladi.  
Bu yerda siz keyingi ekranga ma'lumot uzatishingiz mumkin:

```swift
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if let destinationVC = segue.destination as? ViewController2 {
        // Diqqat: bu yerda IBOutlets hali `nil` bo'lishi mumkin!
        // Masalan: destinationVC.username = "Hello"
    }
}
```

## ⚠️ Muhim eslatma

- `prepare(for:sender:)` metodi chaqirilgan vaqtda, `destinationVC` obyekti mavjud bo‘ladi, **lekin uning `IBOutlet` elementlari hali `nil`** bo‘ladi.
- Buning sababi — bu elementlar faqat `viewDidLoad()` chaqirilgandan keyin yaratiladi.

```swift
// ⚠️ BU XATOLIKKA OLIB KELADI!
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if let destination = segue.destination as? ViewController2 {
        destination.titleLabel.text = "Salom" // ❌ CRASH
    }
}
```

## 📍 To‘g‘ri Yechim

UI elementlar bilan ishlashni `viewDidLoad()` yoki undan keyingi lifecycle metodlariga qoldiring.  
Aks holda, `IBOutlet` lar hali `nil` bo‘lishi mumkin.

Agar siz `prepare(for:)` metodida ma’lumot uzatmoqchi bo‘lsangiz, **faqat model yoki oddiy qiymatlar** yuboring.

```swift
// ✅ TO‘G‘RI YONDASHUV
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if let destination = segue.destination as? ViewController2 {
        destination.userName = "Ali" // faqat model ma’lumot uzatyapmiz
    }
}
```

## 📲 Navigatsiya: `Show` vs `Present Modally`

| Segue turi         | Ekran ko‘rinishi                     | Lifecycle ta’siri                                   |
|---------------------|--------------------------------------|-----------------------------------------------------|
| `Show`              | VC1 orqa fonda yashirin qoladi       | `viewWillDisappear` **ba'zan chaqirilmasligi mumkin** |
| `Present Modally`   | VC2 VC1’ni to‘liq yopadi             | `viewDidDisappear` **doim chaqiriladi**            |

> 🎬 `Modal Presentation` holatida yangi ViewController ekranni to‘liq egallaydi.  
> Shu sababli avvalgi sahifa **butunlay ko‘rinmaydi** va uning lifecycle metodlari **to‘liq ishlaydi**.

---

## ✅ Xulosa

- `viewDidLoad` — View birinchi marta yuklanganda (faqat **1 marta**)
- `viewWillAppear`, `viewDidAppear` — ViewController **har safar ekranda ko‘ringanda**
- `viewWillDisappear`, `viewDidDisappear` — ViewController **har safar yopilganda**
- `prepare(for:)` — Segue orqali boshqa ViewController’ga o‘tilayotganda
  > ℹ️ Ammo `IBOutlet` hali mavjud emas — ehtiyot bo‘ling!

---

## 💡 Pro Tip

Ushbu kod lifecycle metodining nomini avtomatik tarzda chop etadi:

```swift
print(#function)
```

🔍 Debug jarayonida aynan qaysi metodlar va qanday tartibda chaqirilayotganini aniq kuzatish imkonini beradi.  
Bu esa sizga ilova oqimini tushunish va muammolarni topishda juda katta yordam beradi.

---

## 🎓 Yakuniy Tavsiyalar

- Har bir lifecycle metodining **maqsadi** va **chaqirilish vaqtini** chuqur tushunib oling.
- UI o‘zgarishlari, animatsiyalar va ma’lumot uzatishni **tegishli lifecycle bosqichlarida** bajaring.
- `viewDidLoad` — UI bilan ishlash uchun **eng xavfsiz va kafolatli joy** hisoblanadi.
- `prepare(for:)` — faqat **data uzatish** uchun ishlatiladi; hech qachon `IBOutlet` bilan ishlamang.
- Navigatsiya turi (`Show`, `Present Modally`) lifecycle oqimiga **bevosita ta’sir qiladi** — bu holatlarni **test qilib ishonch hosil qiling**.
- Kodni har doim **modulli**, **izchil**, va **toza** yozing — bu **professional ishlab chiquvchining mezonidir**.

---

🎓 _iOS dasturchi sifatida lifecycle’larni chuqur tushunish — sizni kuchliroq, samaraliroq va professionallar safidagi developer’ga aylantiradi._



# 📱 iOS App Lifecycle (Swift)

iOS ilovasi faqatgina ViewController'lar orqali emas, balki butun tizim darajasida ham turli hayotiy bosqichlardan o‘tadi.  
Ushbu hujjatda `AppDelegate`, `SceneDelegate`, va `ViewController` larni o‘z ichiga olgan **to‘liq lifecycle tushunchasi** yoritiladi. 🚀

---

## 🔄 ViewController va App Lifecycle o‘rtasidagi farq

ViewController faqatgina **bitta ekran**ni anglatadi.  
Ammo butun ilovaning hayoti bu ekranlar to‘plamidan **kattaroq** kontekstda qaraladi.

| Komponent        | Nimani anglatadi?            | Misollar                      |
|------------------|-------------------------------|-------------------------------|
| ViewController   | Bitta ekran (UI sahifa)       | Profil sahifasi, Chat ekran  |
| Scene            | Bitta oynadagi VClar to‘plami | iPad multi-window             |
| App              | Butun ilova (foydalanuvchi uchun) | FlashChat, TikTok, Safari     |

---

## 🔥 iOS App Lifecycle Bosqichlari

1. ✅ **Ilova ishga tushadi** – `application(_:didFinishLaunchingWithOptions:)`
2. ✅ **Ilova oynasi ko‘rinadi** – `scene(_:willConnectTo:)`
3. ✅ **Ilova aktiv holatda** – `sceneDidBecomeActive(_:)`
4. ✅ **Ilova passiv bo‘ladi** – `sceneWillResignActive(_:)`
5. ✅ **Ilova background’ga o‘tadi** – `sceneDidEnterBackground(_:)`
6. ✅ **Ilova foreground’ga qaytadi** – `sceneWillEnterForeground(_:)`
7. ✅ **Ilova o‘chiriladi yoki sahna yopiladi** – `application(_:didDiscardSceneSessions:)`

---

## 🧠 Nega bu sizga kerak?

Agar foydalanuvchi forma to‘ldirayotgan bo‘lsa va telefon qo‘ng‘irog‘i bo‘lsa yoki boshqa app ochilsa — siz bu holatlarni `sceneDidEnterBackground` da ushlab, **foydalanuvchi ma'lumotini saqlashingiz kerak**.

Aks holda, **hamma yozilgan narsa yo‘qoladi** ❌

---

## 📁 AppDelegate vs SceneDelegate

### 🔧 AppDelegate.swift — App darajasidagi lifecycle’lar

```swift
func application(_ application: UIApplication,
                 didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    FirebaseApp.configure() // App ishga tushganda
    return true
}
```

```swift
func application(_ application: UIApplication,
                 didDiscardSceneSessions sceneSessions: Set<UISceneSession>) {
    // Ilova sahnasi o‘chirildi
}
```

## 🖼 SceneDelegate.swift — Har bir oynaga xos lifecycle’lar

```swift
func scene(_ scene: UIScene, willConnectTo session: UISceneSession,
           options connectionOptions: UIScene.ConnectionOptions) {
    // Sahna tayyorlanmoqda
}

func sceneDidBecomeActive(_ scene: UIScene) {
    print(#function) // App faol holatga o‘tdi
}

func sceneWillResignActive(_ scene: UIScene) {
    // Ilova passiv holatga o‘tmoqda
}

func sceneDidEnterBackground(_ scene: UIScene) {
    // Ilova background’ga tushdi → foydalanuvchi ma'lumotlarini saqlang!
}

func sceneWillEnterForeground(_ scene: UIScene) {
    // Ilova yana aktiv bo‘lmoqda
}
```

## 📌 Lifecycle Voqealari Zanjiri

```text
1. App Start:
   - AppDelegate.didFinishLaunchingWithOptions
   - SceneDelegate.scene(_:willConnectTo:)
   - SceneDelegate.sceneDidBecomeActive

2. ViewController Display:
   - viewDidLoad
   - viewWillAppear
   - viewDidAppear

3. App to Background (Home button):
   - sceneWillResignActive
   - sceneDidEnterBackground

4. App returns:
   - sceneWillEnterForeground
   - sceneDidBecomeActive

5. App closed:
   - application(_:didDiscardSceneSessions:)
```

## 📦 Yakuniy Xulosa

`App (1) → Scene (1..n) → ViewController (1..n)`

| Qachon?                          | Qayerda?                              |
|----------------------------------|----------------------------------------|
| App ishga tushdi                | AppDelegate                            |
| Firebase sozlash                | AppDelegate                            |
| Ilova oynasi tayyor bo‘ldi     | SceneDelegate                          |
| App aktiv bo‘ldi               | sceneDidBecomeActive                   |
| App passiv / Background bo‘ldi | sceneDidEnterBackground                |
| ViewController ko‘rinmoqda     | viewDidLoad, viewDidAppear             |
| ViewController yopilmoqda      | viewWillDisappear, viewDidDisappear    |

---

## 🎓 Amaliy Misollar

- ✅ **Firebase sozlash:** `AppDelegate.didFinishLaunchingWithOptions`
- ✅ **Forma saqlash:** `SceneDelegate.sceneDidEnterBackground`
- ✅ **Qo‘ng‘iroq paytida audio pauza:** `sceneWillResignActive`
- ✅ **UI animatsiya:** `ViewController.viewDidAppear`
- ✅ **Forma to‘ldirishni tiklash:** `sceneWillEnterForeground`

---

## ✅ Maslahatlar

1. **Lifecycle’larni yaxshi o‘rganing** – harakatlar aniq vaqtida bajariladi.
2. **Kod strukturasini saqlang** – har bir voqeani to‘g‘ri joyda yozing.
3. `print(#function)` bilan **debug qiling** – metodlar qachon chaqirilishini kuzating.
4. **Foydalanuvchi ma'lumotini har doim saqlang** – ayniqsa background’ga o‘tishda.

---

📌 _Tayyor kod yoki misollar kerak bo‘lsa, bemalol yozing._  
📈 _Sizning muvaffaqiyatingiz — to‘g‘ri lifecycle'ni tushunishingizdan boshlanadi._ 🔥

