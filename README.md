# 🛡️ Wi-Fi Intrusion Detection System for IoT with Machine Learning

**Trabajo Fin de Grado — Ingeniería Electrónica de Comunicaciones · UCM**  
**Autor:** José David Conde Quispe · **Tutor:** Guillermo Botella Juan

---

## 📌 Descripción

Sistema de detección de intrusiones (IDS) en redes Wi-Fi orientado a entornos IoT, 
implementado en tiempo real sobre una Raspberry Pi 5. El sistema captura tráfico 
inalámbrico en modo monitor, lo analiza mediante modelos de Machine Learning y 
genera alertas automáticas ante ataques como Deauthentication y Evil Twin.

---

## 🏗️ Arquitectura del sistema
```
[ESP32 C3 Mini Rustboard] ──MQTT──> [AP hostapd · Atheros AR9271]
                                              │
                                    [wlan2 · Modo Monitor]
                                              │
                                    [tshark · captura live]
                                              │ SSH
                                    [Mac · Script Python]
                                              │
                                    [Modelo Random Forest]
                                              │
                                    [🚨 Alerta / 🟢 Normal]
```

---

## 📁 Estructura del repositorio
```
wifi-ids-iot-ml/
├── 1_explorar_dataset.py      # Análisis de archivos AWID (limpios vs infectados)
├── 2_entrenar_deauth.py       # Entrenamiento Random Forest — ataque Deauth
├── 3_entrenar_evil_twin.py    # Entrenamiento Random Forest — ataque Evil Twin
├── 4_isolation_forest.py      # Exploración inicial con Isolation Forest
├── 5_rf_ventanas.py           # RF con ventanas temporales de 50 paquetes
├── 6_captura_live.py          # Captura de tráfico real vía SSH + Raspberry Pi
├── 7_deteccion_tiempo_real.py # Detección en tiempo real con modelo entrenado
└── requirements.txt
```

---

## 🤖 Modelos de Machine Learning

| Modelo | Tipo | Dataset | Ataque |
|--------|------|---------|--------|
| `modelo_deauth.pkl` | Random Forest | AWID3 | Deauthentication |
| `modelo_evil_twin.pkl` | Random Forest | AWID3 | Evil Twin |

### Features utilizadas
| Feature | Descripción |
|---------|-------------|
| `wlan.fc.type` | Tipo de frame Wi-Fi |
| `wlan.fc.subtype` | Subtipo de frame |
| `wlan_radio.signal_dbm` | Potencia de señal (dBm) |
| `frame.len` | Tamaño del paquete |
| `wlan.fc.retry` | Reintento de transmisión |
| `wlan.duration` | Tiempo que reserva el canal |

---

## 🔬 Evolución del enfoque

1. **Isolation Forest** (no supervisado) → descartado por exceso de falsos positivos
2. **Random Forest** (supervisado con AWID3) → modelo final con alta precisión
3. **Ventanas temporales de 50 paquetes** → umbral de 3 paquetes maliciosos para alerta

---

## 🧪 Dataset

Se utiliza el dataset público **AWID3 (Aegean Wireless Intrusion Dataset)**,  
disponible en: https://icsdweb.aegean.gr/awid/

---

## 🛠️ Hardware utilizado

- Raspberry Pi 5 (IP estática `192.168.1.49`)
- 2× Adaptador Wi-Fi Atheros AR9271 (AP + modo monitor)
- ESP32 C3 Mini Rustboard (cliente IoT con sensores vía MQTT)
- ESP32 Marauder (generación de ataques controlados)

---

## ⚙️ Instalación
```bash
pip install pandas scikit-learn joblib
```

Para la captura en tiempo real se requiere acceso SSH a la Raspberry Pi  
con `tshark` instalado y la interfaz en modo monitor.

---

## ⚠️ Aviso

Este proyecto es de investigación académica.  
Las técnicas de ataque se usan exclusivamente en laboratorio controlado.


