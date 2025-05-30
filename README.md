# kubetutorial

## Reflection 1

### 1. Log sebelum & sesudah expose sebagai Service
Sebelum saya menjalankan perintah `kubectl expose`, aplikasi hello-node hanya berjalan di dalam cluster dan tidak bisa diakses dari luar. Saat saya menjalankan `kubectl logs hello-node-c74958b5d-x564h`, output-nya hanya menunjukkan bahwa server HTTP dan UDP sudah aktif.

Setelah saya expose dengan perintah `kubectl expose deployment hello-node --type=LoadBalancer --port=8080` dan mengakses URL dari `minikube service hello-node`, log di pod menunjukkan adanya permintaan yang masuk. Setiap saya me-refresh browser, jumlah log bertambah, menunjukkan bahwa setiap request dari browser tercatat di dalam log container.

### 2. Perbedaan `kubectl get` biasa dan `-n kube-system`
Command `kubectl get` secara default hanya menampilkan resource yang ada di **namespace `default`**. Itulah mengapa pod dan service seperti `metrics-server` tidak muncul.

Sementara saat saya menjalankan `kubectl get pods -n kube-system`, saya bisa melihat pod internal Kubernetes seperti `coredns`, `kube-apiserver`, `metrics-server`, dll. Adanya `-n` digunakan untuk menarget namespace spesifik agar dapat melihat resource yang tidak berada di default namespace.

## Reflection 2
## Refleksi: Rolling Update & Manifest Files

### 1. Perbedaan Rolling Update dan Recreate
- **Rolling Update**: Pod lama akan diganti bertahap dengan pod baru. Ini memungkinkan update tanpa downtime, cocok untuk update minor yang tetap kompatibel.
- **Recreate**: Semua pod lama akan dihentikan terlebih dahulu, baru pod baru dibuat. Strategi ini lebih aman untuk update besar yang tidak kompatibel, namun menyebabkan downtime sementara.

### 2. Deploy dengan Recreate
Saya mengganti strategi deployment pada file `deployment.yaml` dengan:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 1
```

### 4. Manfaat yaml
Penggunaan file manifest yaml membuat proses deployment lebih konsisten dan iteratif. Saya tidak perlu mengingat urutan command `kubectl create` dan `kubectl expose` secara manual. Cukup dengan `kubectl apply -f`, semua konfigurasi akan langsung diterapkan.

Manifest juga memudahkan kolaborasi tim karena dapat di-commit ke Git, ditinjau, dan dipantau histori perubahannya.