# 📱 Laboratoire de Sécurité Android — Analyse Dynamique avec MobSF (DIVA)

> **École :** EMSI &nbsp;|&nbsp; **Filière :** Cybersécurité / Génie Informatique &nbsp;|&nbsp; **Auteur :** Mhttaj zakariyae


---

## 🟢 Étape 1 — Création de l'émulateur AVD sans Play Store (10 min)

L'analyse dynamique avec MobSF nécessite un émulateur **rooté**, ce qui est impossible avec les images Play Store. Il faut donc créer un AVD avec une image **Google APIs** ou **AOSP**.

### Prérequis
- Android Studio installé
- SDK Android avec l'image **API 29 x86** (recommandé)

### Procédure

1. Ouvrir **Android Studio** → `Tools` → `Device Manager`
2. Cliquer sur **Create Device**
3. Choisir un modèle (ex: Pixel 4) → **Next**
4. Sélectionner une image système **sans** l'icône Play Store (ex: `x86 — API 29 — Google APIs`)
5. Cliquer sur **Finish**

```bash
# Vérifier que l'AVD a bien été créé
emulator -list-avds
```

**Résultat attendu :**
```
Pixel_4_API_29
```
<img width="448" height="38" alt="Screenshot 2026-03-24 090226" src="https://github.com/user-attachments/assets/0e13c42a-3fd0-47d1-aa82-2df4ef31cc7b" />


---

## 🟢 Étape 2 — Cloner MobSF pour utiliser les scripts AVD officiels (2 min)

MobSF fournit des scripts officiels pour lancer l'émulateur correctement rooté et prêt pour l'analyse dynamique.

### Cloner le dépôt MobSF

```bash
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
cd Mobile-Security-Framework-MobSF
```

### Explorer les scripts AVD disponibles

```bash
ls scripts/
```

Les scripts importants :

| Script | Rôle |
|--------|------|
| `scripts/android_emulator.sh` | Lance l'émulateur rooté et prêt pour MobSF |
| `scripts/android_tools.sh` | Installe les outils Android nécessaires |

<img width="707" height="158" alt="etape2" src="https://github.com/user-attachments/assets/dda6c573-d4d3-4617-9963-1edfa0ec101c" />

---

## 🟢 Étape 3 — Lancement de l'émulateur avec le script MobSF (rooté & prêt pour l'analyse)

Ce script lance l'émulateur avec les options nécessaires pour l'analyse dynamique (accès root, proxy réseau, frida server...).

### Lancer le script

```bash
cd Mobile-Security-Framework-MobSF
bash scripts/android_emulator.sh <AVD_NAME>
```

**Exemple :**

```bash
bash scripts/android_emulator.sh Pixel_4_API_29
```

### Vérifier la connexion ADB

```bash
adb devices
```

**Résultat attendu :**

```
List of devices attached
emulator-5554   device
```

### Activer le mode root

```bash
adb root
adb remount
```

**Résultat attendu :**

```
remount succeeded
```

> ⚠️ Si `adb remount` échoue, vérifiez que l'image utilisée est bien **Google APIs** et non **Google Play**.

<img width="844" height="269" alt="etape3p1" src="https://github.com/user-attachments/assets/3f2e5c4c-fcb9-4394-ab07-6782ea63f158" />
<img width="739" height="702" alt="etape3p2" src="https://github.com/user-attachments/assets/b0915832-da79-4cc4-8a7b-23f315c0ba29" />
<img width="1045" height="559" alt="etape3p3" src="https://github.com/user-attachments/assets/a3fca542-556e-4f6c-b0b2-e3051b1805cd" />



---

## 🟢 Étape 4 — Installation et lancement de MobSF via Docker (5 min)

Docker est la méthode la plus simple pour lancer MobSF sans configuration complexe.

### Prérequis

- Docker installé et démarré sur votre machine

### Télécharger et lancer MobSF

```bash
docker pull opensecurity/mobile-security-framework-mobsf:latest

docker run -it --rm \
  -p 8000:8000 \
  -p 1337:1337 \
  opensecurity/mobile-security-framework-mobsf:latest
```

### Accéder à l'interface MobSF

Ouvrez votre navigateur et accédez à :

```
http://127.0.0.1:8000
```

**Identifiants par défaut :**

| Champ | Valeur |
|-------|--------|
| Utilisateur | `mobsf` |
| Mot de passe | `mobsf` |


---

## 🟢 Étape 5 — Téléchargement de l'APK DIVA (Damn Insecure and Vulnerable App)

DIVA est une application Android volontairement vulnérable, conçue pour apprendre la sécurité mobile.

### Télécharger l'APK

```bash
# Option 1 : via wget
wget https://github.com/payatu/diva-android/raw/master/DivaApplication.apk -O diva.apk

# Option 2 : via curl
curl -L https://github.com/payatu/diva-android/raw/master/DivaApplication.apk -o diva.apk
```

### Installer DIVA sur l'émulateur

```bash
adb install diva.apk
```

**Résultat attendu :**

```
Performing Streamed Install
Success
```

- Ouvrir l'application **DIVA** depuis l'écran d'accueil de l'émulateur


---

## 🟢 Étape 6 — Analyse Statique + Dynamique de DIVA (le cœur du lab)

### 6.1 Analyse Statique

1. Dans l'interface MobSF (`http://127.0.0.1:8000`), glissez-déposez le fichier `diva.apk`
2. MobSF décompile automatiquement l'APK et génère un rapport complet
3. Consultez les sections suivantes dans le rapport :

| Section | Ce qu'on cherche |
|---------|-----------------|
| Security Score | Score global de sécurité |
| Permissions | Permissions dangereuses déclarées |
| Certificate | Informations sur le certificat |
| Hardcoded Secrets | Identifiants / clés en dur dans le code |
| Manifest Issues | Mauvaises configurations AndroidManifest.xml |

<img width="1280" height="726" alt="etape6p1" src="https://github.com/user-attachments/assets/840e7d01-03af-4011-beef-e1b62968f779" />

---

### 6.2 Analyse Dynamique

1. Dans MobSF, cliquer sur **Dynamic Analysis**
2. Sélectionner l'émulateur connecté (`emulator-5554`)
3. Cliquer sur **Start Dynamic Analysis**
4. MobSF instrumente l'application et surveille son comportement en temps réel

---

#### 🔴 Test 1 — Journalisation Non Sécurisée (Insecure Logging)

**Dans DIVA :** Ouvrir `Insecure Logging` → saisir des données → valider

```bash
adb logcat | grep -i diva
```

**Observation :** Les identifiants apparaissent en clair dans les logs :

```
D/DIVA: Credentials: username=admin password=1234
```

**⚠️ Risque :** Un attaquant ayant accès à l'appareil peut récupérer les identifiants directement depuis les journaux système.
on click sur cette icon pour commence lanalyse
<img width="306" height="75" alt="image" src="https://github.com/user-attachments/assets/f4ca04c4-c088-4d4d-a032-27795047916c" />

---

#### 🔴 Test 2 — Identifiants en Dur (Hardcoded Credentials)

**Dans DIVA :** Ouvrir `Hardcoded Issues`

**Observation via MobSF :** Des clés secrètes sont codées en dur dans le code Java :

```java
String vendorKey = "VendorKey123";
String secret    = "SUPERSecret@123";
```

**⚠️ Risque :** La rétro-ingénierie de l'APK expose immédiatement les secrets via `jadx` ou `apktool`.


---

#### 🟠 Test 3 — Stockage Non Sécurisé (Insecure Data Storage)

**Dans DIVA :** Ouvrir `Insecure Data Storage` → saisir et enregistrer des données

```bash
adb shell
cd /data/data/jakhar.aseem.diva/
ls
cat shared_prefs/jakhar.aseem.diva_preferences.xml
```

**Observation :** Données stockées en texte clair :

```xml
<string name="password">MonMotDePasse123</string>
```

**⚠️ Risque :** Sur un appareil compromis, un attaquant peut lire directement tous les fichiers de l'application.


---

#### 🟠 Test 4 — Vulnérabilité WebView

**Dans DIVA :** Ouvrir `Input Validation Issues` → saisir une URL arbitraire

**Observation :** L'application charge l'URL sans validation dans son WebView interne.

**⚠️ Risque :** XSS, phishing ou exécution de code malveillant depuis l'intérieur de l'application.


---

#### 🟡 Test 5 — Cryptographie Faible (Weak Cryptography)

**Observation via MobSF :** Utilisation de `MD5` ou `DES` détectée dans le code.

**⚠️ Risque :** Les données chiffrées peuvent être décryptées facilement avec des outils modernes.


---

### 6.3 Tableau Récapitulatif des Vulnérabilités

| Vulnérabilité | Sévérité | Détecté par |
|---------------|----------|-------------|
| Journalisation non sécurisée | 🔴 CRITIQUE | ADB Logcat |
| Identifiants en dur | 🔴 HAUTE | MobSF Statique |
| Stockage non sécurisé | 🔴 HAUTE | ADB Shell |
| Vulnérabilité WebView | 🟠 HAUTE | MobSF Dynamique |
| Cryptographie faible | 🟡 MOYENNE | MobSF Statique |
| Application debuggable | 🟡 MOYENNE | MobSF Statique |


---

## 🔬 Étape 7 — Tests avancés & exploration (à faire vous-même)

Ces tests sont laissés à votre initiative pour approfondir votre compréhension :

- [ ] **Access Control Issues** : tester les contrôles d'accès insuffisants dans DIVA
- [ ] **Input Validation Issues** : tester les injections SQL dans DIVA
- [ ] **Intercepter le trafic réseau** avec Burp Suite + proxy MobSF
- [ ] **Analyser les appels API** avec Frida via MobSF Dynamic Analysis
- [ ] **Générer le rapport PDF complet** depuis MobSF et l'annoter
- [ ] **Comparer** les résultats statique vs dynamique

> 💡 *Documentez vos observations avec des captures d'écran pour chaque test.*

---

## 🛠️ Dépannage Rapide (si quelque chose coince)

| Problème | Erreur | Solution |
|----------|--------|----------|
| `adb remount` échoue | `remount failed` | Utiliser un AVD **sans Play Store** (Google APIs uniquement) |
| MobSF ne détecte pas l'émulateur | Timeout connexion | Lancer `adb root` avant de démarrer l'analyse dynamique |
| Docker ne démarre pas | Port 8000 occupé | Changer le port : `-p 8001:8000` et accéder via `localhost:8001` |
| APK non analysé | Upload échoue | Vérifier la taille du fichier (max 50 MB) et le format `.apk` |
| Émulateur trop lent | Freeze / lag | Activer la **virtualisation matérielle** (Intel HAXM ou KVM) |
| API trop récente | Accès root refusé | Revenir à l'**API 29** ou inférieur |

---

## 🏁 Conclusion & Prochaine Étape

### Ce que nous avons accompli

- ✅ Créé un émulateur Android rooté compatible MobSF
- ✅ Cloné et utilisé les scripts officiels MobSF AVD
- ✅ Déployé MobSF via Docker
- ✅ Analysé l'APK DIVA statiquement et dynamiquement
- ✅ Identifié les principales vulnérabilités mobiles (OWASP Mobile Top 10)
- ✅ Utilisé ADB pour explorer les données stockées sur l'appareil

### Prochaines étapes suggérées

- 🔜 Tester une application réelle avec les mêmes techniques
- 🔜 Apprendre **Frida** pour le hooking dynamique
- 🔜 Explorer **OWASP Mobile Top 10** en profondeur
- 🔜 Pratiquer sur [HackTheBox](https://www.hackthebox.com/) ou [TryHackMe](https://tryhackme.com/)

---

## 📎 Références

- [OWASP Mobile Top 10](https://owasp.org/www-project-mobile-top-10/)
- [Documentation officielle MobSF](https://mobsf.github.io/Mobile-Security-Framework-MobSF/)
- [DIVA Android — GitHub](https://github.com/payatu/diva-android)
- [Android Security Best Practices](https://developer.android.com/topic/security/best-practices)
- [Frida — Dynamic Instrumentation](https://frida.re/)

---

<div align="center">
  <sub>Réalisé dans le cadre du cours de Cybersécurité — EMSI</sub>
</div>
