# Laporan Latihan Modul 2 Sistem Operasi

## Identitas Diri

- **NRP**     : 5025241186
- **Nama**    : Dyah Utami Kesuma Dewi 
- **Kelas**   : E
- **Kelompok**: 07

---

## Program yang Telah Dibuat

### 🔹 1. Process

**Deskripsi:**

Buat satu program C yang melakukan tiga proses secara berurutan:
a. Membuat folder baru bernama `halo`.
b. Membuat file kosong bernama `hai.txt` di dalam folder `halo`.
c. Mengcopy file `hai.txt` tersebut ke luar folder, sehingga hasilnya:

- Ada `hai.txt` di dalam folder `halo/`
- Ada juga salinan `hai.txt` di luar folder `halo` (di direktori yang sama dengan program ini dijalankan)

**Kode Program:**

```
#include <stdio.h>
#include <stdlib.h>
#include <sys/stat.h>

int main() {
    mkdir("halo", 0777);

    FILE *file = fopen("halo/hai.txt", "w");
    fclose(file);

    FILE *asal = fopen("halo/hai.txt", "r");
    FILE *tujuan = fopen("hai.txt", "w");

    char ch;
    while ((ch = fgetc(asal)) != EOF) {
        fputc(ch, tujuan);
    }

    fclose(asal);
    fclose(tujuan);

    return 0;
}
```

**Penjelasan program:**

Program ini menggunakan bahasa C dan bertujuan untuk:
1. Membuat folder baru bernama halo.
2. Membuat file kosong bernama hai.txt di dalam folder halo.
3. Menyalin isi dari halo/hai.txt ke file baru bernama hai.txt di direktori utama.

```
mkdir("halo", 0777);
```
Membuat direktori bernama `halo` dengan permission `0777` (bisa dibaca, ditulis, dan dijalankan oleh semua user).

```
FILE *file = fopen("halo/hai.txt", "w");
fclose(file);
```
Membuka (atau membuat jika belum ada) file bernama `hai.txt` di dalam folder halo untuk ditulis. Karena tidak ada isi yang ditulis, file ini kosong. Kemudian file langsung ditutup.

```
FILE *asal = fopen("halo/hai.txt", "r");
FILE *tujuan = fopen("hai.txt", "w");
```
Membuka file `halo/hai.txt` untuk dibaca `(r)` dan membuka (atau membuat) `file hai.txt` untuk ditulis `(w)` di folder saat ini.

```
char ch;
while ((ch = fgetc(asal)) != EOF) {
    fputc(ch, tujuan);
}
```
Membaca setiap karakter dari file `halo/hai.txt` lalu menuliskannya ke file `hai.txt`. Karena file `halo/hai.txt` kosong, maka tidak ada karakter yang disalin.

```
fclose(asal);
fclose(tujuan);
```
Menutup kedua file setelah proses salin selesai.

**Hasil output:**

1. Folder baru halo berhasil dibuat.
2. Di dalam folder halo, terdapat file kosong `hai.txt`.
3. Di direktori utama (tempat kamu menjalankan program), akan ada file `hai.txt` yang juga kosong karena menyalin dari file kosong.

**Penjelasan output:**

Karena file `halo/hai.txt` dibuat dalam mode tulis `("w")` tapi tidak ada data yang dituliskan ke dalamnya, maka file tersebut tetap kosong.

Saat program mencoba menyalin isinya ke `hai.txt`, proses penyalinan tetap berjalan, tapi karena tidak ada isi dalam `halo/hai.txt`, maka `hai.txt` juga menjadi file kosong.

**Screenshot hasil output:**
![Image](https://github.com/user-attachments/assets/03f71206-6555-4489-a464-cc4e9106fa3a)
![Image](https://github.com/user-attachments/assets/94e3f253-5cce-4f97-8181-379c6a20cda7)
![Image](https://github.com/user-attachments/assets/6de0a4e3-d188-466d-a7e7-38704fd28230)
![Image](https://github.com/user-attachments/assets/42caf5b8-bef7-4bd5-a965-75738e515e33)

### 🔹 2. Thread

**Deskripsi:**

Buat satu program dengan **3 thread**, masing-masing bertugas:
a. **Thread 1:** Menulis angka 1–100 secara berurutan ke file `count.txt`.
b. **Thread 2:** Menulis `"Saya pintar mengerjakan thread"` ke file `print.txt`.
c. **Thread 3:** Menulis semua angka **genap** dari 1–100 ke file `count_2.txt`.

**Kode Program:**

```
#include <stdio.h>
#include <pthread.h>

void* thread1_func(void* arg) {
    FILE *file = fopen("count.txt", "w");
    for (int i = 1; i <= 100; i++) {
        fprintf(file, "%d\n", i);
    }
    fclose(file);
    return NULL;
}

void* thread2_func(void* arg) {
    FILE *file = fopen("print.txt", "w");
    fprintf(file, "Saya pintar mengerjakan thread\n");
    fclose(file);
    return NULL;
}

void* thread3_func(void* arg) {
    FILE *file = fopen("count_2.txt", "w");
    for (int i = 2; i <= 100; i += 2) {
        fprintf(file, "%d\n", i);
    }
    fclose(file);
    return NULL;
}

int main() {
    pthread_t t1, t2, t3;

    pthread_create(&t1, NULL, thread1_func, NULL);
    pthread_join(t1, NULL);

    pthread_create(&t2, NULL, thread2_func, NULL);
    pthread_join(t2, NULL);

    pthread_create(&t3, NULL, thread3_func, NULL);
    pthread_join(t3, NULL);

    FILE *logFile = fopen("log.txt", "a");
    if (logFile == NULL) {
        printf("Gagal membuka log.txt\n");
        return 1;
    }

    fprintf(logFile, "1. Thread 1\n");
    fprintf(logFile, "2. Thread 2\n");
    fprintf(logFile, "3. Thread 3\n");

    fclose(logFile);

    return 0;
}
```

**Penjelasan Program:**

Program ini menggunakan tiga buah thread untuk melakukan tugas-tugas yang berbeda secara paralel. Namun, dalam implementasi ini, ketiga thread dijalankan secara berurutan karena setiap thread langsung dijalankan dan ditunggu (pthread_join) sebelum thread berikutnya dibuat. Berikut rincian tugas dari masing-masing thread:
Thread 1: Menulis angka 1–100 ke dalam file `count.txt`.
Thread 2: Menulis kalimat `"Saya pintar mengerjakan thread"` ke dalam file `print.txt`.
Thread 3: Menulis angka genap dari 2–100 ke dalam file `count_2.txt`.
Setelah semua thread selesai, program utama menulis urutan eksekusi ke dalam file `log.txt`.

```
#include <stdio.h>
#include <pthread.h>
```
Mengimpor library yang diperlukan: `stdio.h` untuk input/output dan `pthread.h` untuk thread.

```
void* thread1_func(void* arg) {
    FILE *file = fopen("count.txt", "w");
    for (int i = 1; i <= 100; i++) {
        fprintf(file, "%d\n", i);
    }
    fclose(file);
    return NULL;
}
```
`thread1_func` adalah fungsi untuk menulis angka 1 hingga 100 ke dalam file `count.txt`.

```
void* thread2_func(void* arg) {
    FILE *file = fopen("print.txt", "w");
    fprintf(file, "Saya pintar mengerjakan thread\n");
    fclose(file);
    return NULL;
}
```
`thread2_func` menulis kalimat ke file `print.txt`.

```
void* thread3_func(void* arg) {
    FILE *file = fopen("count_2.txt", "w");
    for (int i = 2; i <= 100; i += 2) {
        fprintf(file, "%d\n", i);
    }
    fclose(file);
    return NULL;
}
```
`thread3_func` menulis angka genap dari 2 hingga 100 ke file `count_2.txt`.

```
pthread_create(...);
pthread_join(...);
```
Digunakan untuk membuat dan menunggu thread sampai selesai.

```
FILE *logFile = fopen("log.txt", "a");
if (logFile == NULL) {
        printf("Gagal membuka log.txt\n");
        return 1;
    }
fprintf(logFile, "1. Thread 1\n");
fprintf(logFile, "2. Thread 2\n");
fprintf(logFile, "3. Thread 3\n");
```
Menulis urutan thread yang selesai ke file `log.txt`

**Hasil Output:**

Setelah dijalankan, program akan menghasilkan empat file:
`count.txt` – Berisi angka 1 sampai 100, satu per baris.
`print.txt` – Berisi tulisan Saya pintar mengerjakan thread.
`count_2.txt` – Berisi angka genap dari 2 hingga 100.
`log.txt` – Berisi log urutan thread yang selesai. Karena thread dijalankan satu-satu, urutannya tetap:

```
1. Thread 1
2. Thread 2
3. Thread 3
```

**Penjelasan Hasil Output:**

1. File `count.txt` menunjukkan bahwa thread pertama berhasil mencetak angka 1–100.
2. File `print.txt` menunjukkan bahwa thread kedua berhasil mencetak kalimat yang diminta.
3. File `count_2.txt` menunjukkan bahwa thread ketiga berhasil mencetak angka genap dari 2–100.
4. File `log.txt` berisi urutan penyelesaian thread, yang dalam kasus ini selalu sama karena setiap thread dijalankan secara berurutan, bukan paralel.

**Screenshot Hasil Output:**



### 🔹 3. Thread dengan Mutex

**Deskripsi:**

Buatlah satu program yang:
a. Membuat **5 thread**, masing-masing menghitung dari 1 sampai 3.
b. Setiap thread menulis hasil hitungannya ke file `log.txt` dengan format:
```
thread {id} count {angka}
```
c. Gunakan `pthread_mutex` untuk mencegah **race condition** saat menulis ke file.

**Kode Program:**

```
#include <stdio.h>
#include <pthread.h>

pthread_mutex_t mutex;

void* thread_func(void* arg) {
    int id = *((int*)arg);
    FILE *file = fopen("log.txt", "a");

    if (file == NULL) {
        printf("Gagal membuka file\n");
        return NULL;
    }

    for (int i = 1; i <= 3; i++) {

        pthread_mutex_lock(&mutex);

        fprintf(file, "thread %d count %d\n", id, i);

        pthread_mutex_unlock(&mutex);
    }

    fclose(file);
    return NULL;
}

int main() {
    pthread_t threads[5];
    int thread_ids[5] = {1, 2, 3, 4, 5};

    pthread_mutex_init(&mutex, NULL);

    for (int i = 0; i < 5; i++) {
        pthread_create(&threads[i], NULL, thread_func, &thread_ids[i]);
    }

    for (int i = 0; i < 5; i++) {
        pthread_join(threads[i], NULL);
    }

    pthread_mutex_destroy(&mutex);

    return 0;
}
```

**Penjelasan Program:**

Program ini menggunakan 5 thread yang bekerja secara bersamaan untuk menulis informasi ke dalam sebuah file bernama `log.txt`. Untuk menghindari konflik saat banyak thread menulis ke file yang sama, digunakan mutex (mutual exclusion) agar hanya satu thread yang bisa menulis pada satu waktu.

```
#include <stdio.h>
#include <pthread.h>
```
Mengimpor pustaka untuk input/output `(stdio.h)` dan pemrograman multithread `(pthread.h)`.

```
pthread_mutex_t mutex;
```
Deklarasi sebuah mutex untuk sinkronisasi thread saat menulis ke file.

```
void* thread_func(void* arg) {
    int id = *((int*)arg); // Mendapatkan ID thread
    FILE *file = fopen("log.txt", "a"); // Membuka file log.txt dalam mode append

    if (file == NULL) {
        printf("Gagal membuka file\n");
        return NULL;
    }

    for (int i = 1; i <= 3; i++) {
        pthread_mutex_lock(&mutex); // Mengunci akses sebelum menulis

        fprintf(file, "thread %d count %d\n", id, i);

        pthread_mutex_unlock(&mutex); // Membuka akses setelah menulis
    }

    fclose(file); // Menutup file
    return NULL;
}
```
Fungsi ini akan dijalankan oleh tiap thread. Setiap thread menulis tiga baris ke file `log.txt`, berisi ID thread dan nomor count-nya.

```
int main() {
    pthread_t threads[5];
    int thread_ids[5] = {1, 2, 3, 4, 5};

    pthread_mutex_init(&mutex, NULL); // Inisialisasi mutex

    for (int i = 0; i < 5; i++) {
        pthread_create(&threads[i], NULL, thread_func, &thread_ids[i]); // Membuat thread
    }

    for (int i = 0; i < 5; i++) {
        pthread_join(threads[i], NULL); // Menunggu semua thread selesai
    }

    pthread_mutex_destroy(&mutex); // Menghancurkan mutex
    return 0;
}
```
Program utama membuat 5 thread, masing-masing menjalankan fungsi `thread_func`, dan menunggu semua thread selesai sebelum keluar.

**Hasil Output:**

Isi dari file `log.txt` akan tampak seperti ini (urutan bisa berbeda-beda tiap kali dijalankan):

```
thread 1 count 1
thread 1 count 2
thread 1 count 3
thread 3 count 1
thread 3 count 2
thread 3 count 3
thread 2 count 1
thread 2 count 2
thread 2 count 3
thread 4 count 1
thread 4 count 2
thread 4 count 3
thread 5 count 1
thread 5 count 2
thread 5 count 3
```

**Penjelasan Hasil Output:**

1. Setiap thread mencetak 3 baris output ke dalam log.txt, sehingga total ada 15 baris.
2. Format output: thread [id] count [i] di mana [id] adalah ID thread (1–5) dan [i] adalah hitungan 1–3.
3. Karena digunakan pthread_mutex, tidak ada konflik atau tumpang-tindih saat menulis ke file, meskipun thread berjalan secara paralel.
4. Urutan thread bisa berbeda-beda setiap dijalankan karena eksekusi thread bersifat tidak deterministik.




### 🔹 4. IPC

**Deskripsi:**

### a. Shared Memory

**Buat dua file C:**

- `sender.c`: Membuat shared memory dan mengirim pesan:
  ```
  aku lagi belajar ipc
  ```
- `receiver.c`: Membaca pesan dari shared memory dan menampilkannya ke layar.

**Kode Program:**

**sender.c**
```
#include <stdio.h>
#include <stdlib.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <string.h>

int main() {
    key_t key = ftok("sender.c", 65);
    int shmid = shmget(key, 1024, 0666 | IPC_CREAT);
    if (shmid == -1) { 
        perror("shmget failed");
        exit(1);
    }

    char *str = (char*) shmat(shmid, NULL, 0);
    if (str == (char*)-1) {
        perror("shmat failed");
        exit(1);
    }

    strcpy(str, "aku lagi belajar ipc");

    printf("Pesan telah dikirim ke shared memory: %s\n", str);

    shmdt(str);

    return 0;
}
```

**Penjelasan Kode Program:**

Program ini mengirimkan pesan ke shared memory yang dapat diakses oleh proses lain. Berikut adalah penjelasan detail tentang kode tersebut:
```
#include <stdio.h>
#include <stdlib.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <string.h>
```
Header ini mengimpor pustaka yang diperlukan untuk komunikasi antar proses (IPC), seperti `shmget`, `shmat`, dan `shmdt`.

```
key_t key = ftok("sender.c", 65);
```
Fungsi `ftok` menghasilkan sebuah key unik berdasarkan nama file `(sender.c)` dan angka `(65)`. Key ini digunakan untuk membuat dan mengakses shared memory.

```
int shmid = shmget(key, 1024, 0666 | IPC_CREAT);
if (shmid == -1) {
    perror("shmget failed");
    exit(1);
}
```
Fungsi `shmget` digunakan untuk mengalokasikan shared memory dengan ID `shmid`. `1024` adalah ukuran memori yang akan dialokasikan (1024 bytes). `0666` adalah izin akses file, dan `IPC_CREAT` berarti membuat shared memory jika belum ada.

```
char *str = (char*) shmat(shmid, NULL, 0);
if (str == (char*)-1) {
    perror("shmat failed");
    exit(1);
}
```
Fungsi shmat menghubungkan shared memory ke ruang alamat proses. Hasilnya adalah pointer str yang menunjuk ke shared memory.

```
strcpy(str, "aku lagi belajar ipc");
```
Pesan `"aku lagi belajar ipc"` disalin ke dalam shared memory melalui pointer str.

```
printf("Pesan telah dikirim ke shared memory: %s\n", str);
```
Pesan yang dikirimkan ke shared memory dicetak ke layar.

```
shmdt(str);
```
Fungsi `shmdt` digunakan untuk melepaskan shared memory setelah proses selesai mengaksesnya.

**receiver.c**
```
#include <stdio.h>
#include <stdlib.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <string.h>

int main() {
    key_t key = ftok("sender.c", 65);
    int shmid = shmget(key, 1024, 0666 | IPC_CREAT);
    char *str = (char*) shmat(shmid, NULL, 0);

    printf("Pesan dari shared memory: %s\n", str);

    shmdt(str);

    shmctl(shmid, IPC_RMID, NULL);

    return 0;
}
```

**Penjelasan Kode Program:**

Program ini membaca pesan dari shared memory yang telah dikirimkan oleh `sender.c` dan menampilkannya ke layar.

```
#include <stdio.h>
#include <stdlib.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <string.h>
```
Header yang sama dengan `sender.c` untuk menangani shared memory.

```
key_t key = ftok("sender.c", 65);
```
`ftok` menghasilkan key yang sama seperti di `sender.c`, yang digunakan untuk mengakses shared memory.

```
int shmid = shmget(key, 1024, 0666 | IPC_CREAT);
char *str = (char*) shmat(shmid, NULL, 0);
```
`shmget` digunakan untuk mengambil ID shared memory yang sama dengan yang ada di `sender.c`. `shmat` menghubungkan shared memory ke ruang alamat proses.

```
printf("Pesan dari shared memory: %s\n", str);
```
Pesan yang ada di shared memory (yang dikirimkan oleh `sender.c`) dibaca dan ditampilkan.

```
shmdt(str);
```
Fungsi `shmdt` digunakan untuk melepaskan shared memory setelah selesai mengaksesnya.

```
shmctl(shmid, IPC_RMID, NULL);
```
Fungsi `shmctl` digunakan untuk menghapus shared memory setelah proses selesai menggunakannya.

**Hasil Output:**

Saat program `sender.c` dijalankan, output yang ditampilkan di layar adalah:
```
Pesan telah dikirim ke shared memory: aku lagi belajar ipc
```
Program ini akan mengirim pesan tersebut ke shared memory.

Saat program `receiver.c` dijalankan, output yang ditampilkan di layar adalah:
```
Pesan dari shared memory: aku lagi belajar ipc
```
Program ini akan membaca pesan yang telah disalin oleh `sender.c` ke shared memory dan menampilkannya.

**Penjelasan Hasil Output:**

1. Program sender.c mengirimkan pesan "aku lagi belajar ipc" ke shared memory dan mencetaknya ke layar. Pesan ini bisa diakses oleh proses lain yang memiliki akses ke shared memory yang sama.
2. Program receiver.c mengambil pesan yang telah dikirim ke shared memory oleh sender.c dan menampilkannya ke layar.

Kesimpulan:
1. Inter-process Communication (IPC) menggunakan shared memory memungkinkan komunikasi antar proses dalam sistem operasi yang sama dengan cara berbagi memori bersama.
2. Program sender.c mengirimkan pesan ke memori yang dibaca oleh receiver.c.

**Screenshot Hasil Output:**








### b. Message Queue

- Buat program yang menggunakan **message queue**.
- Kirimkan pesan:
  ```
  yah belajar ipc mulu
  ```
- Setelah itu, program membaca pesan dari queue dan menampilkannya ke layar.

**Kode Program:**

**sender.c**
```
#include <stdio.h>
#include <stdlib.h>
#include <sys/ipc.h>
#include <sys/msg.h>
#include <string.h>

struct msg_buffer {
    long msg_type;
    char msg_text[100];
};

int main() {
    key_t key = ftok("sender.c", 65);
    int msgid = msgget(key, 0666 | IPC_CREAT);

    struct msg_buffer message;
    message.msg_type = 1;
    strcpy(message.msg_text, "yah belajar ipc mulu");

    msgsnd(msgid, &message, sizeof(message), 0);

    printf("Pesan telah dikirim ke message queue: %s\n", message.msg_text);

    return 0;
}
```

**Penjelasan Kode Program:**

Program ini mengirimkan pesan ke message queue menggunakan struktur IPC (Inter-process Communication).

```
struct msg_buffer {
    long msg_type;
    char msg_text[100];
};
```
Struktur `msg_buffer` berfungsi sebagai format pesan. `msg_type` harus bertipe long dan digunakan untuk mengkategorikan pesan. `msg_text` adalah isi pesan.

```
key_t key = ftok("sender.c", 65);
int msgid = msgget(key, 0666 | IPC_CREAT);
```
`ftok` menghasilkan key unik berdasarkan nama file `(sender.c)` dan angka `(65)`.
`msgget` membuat atau mengambil ID dari message queue berdasarkan key tersebut.

```
message.msg_type = 1;
strcpy(message.msg_text, "yah belajar ipc mulu");
```
`msg_type` diset ke 1.
Pesan `"yah belajar ipc mulu"` dimasukkan ke `msg_text`.

```
msgsnd(msgid, &message, sizeof(message), 0);
```
Mengirim pesan ke message queue.
`sizeof(message)` digunakan untuk menentukan panjang data yang dikirim.

```
printf("Pesan telah dikirim ke message queue: %s\n", message.msg_text);
```
Menampilkan pesan yang telah dikirim.

**receiver.c**
```
#include <stdio.h>
#include <stdlib.h>
#include <sys/ipc.h>
#include <sys/msg.h>
#include <string.h>

struct msg_buffer {
    long msg_type;
    char msg_text[100];
};

int main() {
    key_t key = ftok("sender.c", 65);
    int msgid = msgget(key, 0666 | IPC_CREAT);

    struct msg_buffer message;

    msgrcv(msgid, &message, sizeof(message), 1, 0);

    printf("Pesan diterima dari message queue: %s\n", message.msg_text);

    msgctl(msgid, IPC_RMID, NULL);

    return 0;
}
```

**Penjelasan Kode Program:**

Program ini membaca pesan dari message queue.

```
key_t key = ftok("sender.c", 65);
int msgid = msgget(key, 0666 | IPC_CREAT);
```
Menghasilkan key dan mengambil message queue dengan key yang sama seperti di `sender.c`.

```
msgrcv(msgid, &message, sizeof(message), 1, 0);
```
Menerima pesan dengan `msg_type = 1`.
`sizeof(message)` adalah panjang pesan yang diterima.

```
printf("Pesan diterima dari message queue: %s\n", message.msg_text);
```
Menampilkan pesan yang diterima dari message queue.

```
msgctl(msgid, IPC_RMID, NULL);
```
Menghapus message queue dari sistem.

**Hasil Output:**

Saat menjalankan `sender.c` :
```
./sender
Pesan telah dikirim ke message queue: yah belajar ipc mulu
```

Saat menjalankan `receiver.c` :
```
./receiver
Pesan diterima dari message queue: yah belajar ipc mulu
```

**Penjelasan Hasil Output:**

1. `sender.c` mengirimkan pesan "yah belajar ipc mulu" ke message queue.
2. `receiver.c` membaca pesan tersebut dari queue dan mencetaknya.
3. Setelah dibaca, queue dihapus menggunakan `msgctl()`.

**Screenshot Hasil Output:**







### c. Pipe dengan `fork()`

- Gunakan **pipe** dan **`fork()`** untuk membuat _child process_.
- _Parent process_ mengirim string:
  ```
  hai, anak sisop 24
  ```
- _Child process_ menerima pesan dan menampilkannya ke layar.

**Kode Program:**

```
#include <stdio.h>
#include <unistd.h>
#include <string.h>

int main() {
    int fd[2];
    pid_t pid;
    char pesan[] = "hai, anak sisop 24";
    char buffer[100];

    pipe(fd);
    pid = fork();

    if (pid > 0) {
        close(fd[0]);
        write(fd[1], pesan, strlen(pesan) + 1);
        close(fd[1]);
    } else {
        close(fd[1]);
        read(fd[0], buffer, sizeof(buffer));
        printf("Child menerima pesan: %s\n", buffer);
        close(fd[0]);
    }

    return 0;
}
```

**Penjelasan Kode Program:**

`int fd[2];`
Membuat array untuk menyimpan file descriptor pipe. `fd[0]` digunakan untuk membaca, `fd[1]` untuk menulis.

`pipe(fd);`
Membuat pipe sebagai media komunikasi antar proses.

`pid = fork();`
Membuat proses anak (child process). `pid > 0` menandakan parent process, dan `pid == 0` menandakan child process.

Parent Process `(pid > 0)`:
- Menutup ujung baca pipe `(fd[0])`.
- Menulis pesan `"hai, anak sisop 24"` ke pipe melalui `fd[1]`.
- Menutup ujung tulis setelah selesai.

Child Process `(pid == 0)`:
- Menutup ujung tulis pipe `(fd[1])`.
- Membaca data dari pipe menggunakan `read()` ke buffer.
- Menampilkan pesan yang diterima dengan `printf`.

**Hasil Output:**

```
Child menerima pesan: hai, anak sisop 24
```

**Penjelasan Hasil Output:**

1. Proses parent berhasil mengirim pesan `"hai, anak sisop 24"` ke pipe.
2. Proses child membaca pesan tersebut dari pipe dan mencetaknya ke layar.
3. Menunjukkan bahwa komunikasi antar proses menggunakan pipe telah berhasil dilakukan.

**Screenshot Hasil Output:**

