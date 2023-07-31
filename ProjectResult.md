## Latar Belakang
Kelembapan udara adalah banyaknya uap air dalam atmosfer, berasal dari penguapan air dan transpirasi tumbuhan. Indonesia, terutama DKI Jakarta, memiliki temperatur udara tinggi karena wilayah tropis dan perkembangan urbanisasi yang intensif. Kelembapan udara penting untuk kenyamanan dan kesehatan manusia. Penelitian ini bertujuan untuk memprediksi tingkat kelembapan udara DKI Jakarta pada tanggal 18-22 Desember 2021 berdasarkan data dari 1 November hingga 17 Desember 2021, dengan harapan memberikan informasi bermanfaat untuk masyarakat dan meningkatkan kesadaran akan kualitas udara di sekitar DKI Jakarta.

## Data dan Variabel
Data yang digunakan dalam penelitian ini adalah data sekunder yang diperoleh dari data laporan iklim harian Provinsi DKI Jakarta oleh Badan Meteorologi, Klematologi, dan Geofisika (BMKG) Stasiun Meteorologi Kemayoran pada tahun 2021 melalui https://dataonline.bmkg.go.id/.

Pada penelitian ini, variabel yang diamati adalah data rata-rata tingkat kelembapan udara rata-rata (dalam persen) di Provinsi DKI Jakarta. Data yang digunakan adalah data observasi pada tanggal 1 November 2021 – 17 Desember 2021 (sebanyak 47 observasi).

## Alur Penelitian
![Alur Penelitian](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/5d466963-f8c8-4d80-bf17-c6585dfc7334)

## Uji Kestasioneran Data
Dari plot _time series_, secara objektif data memiliki _trend_ sehingga dapat diasumsikan bahwa data tidak stasioner. Untuk memperkuat asumsi tersebut akan dilakukan uji stasioner menggunakan uji _Augmented of Dicky-Fuller_ (ADF). 

```
        Augmented Dickey-Fuller Test

data:  rhavg
Dickey-Fuller = -2.4571, Lag order = 3, p-value = 0.3919
alternative hypothesis: stationary
```

Dari hasil tersebut, dapat disimpulkan bahwa pada tingkat signifikansi 0.05, data tidak stasioner atau memiliki _unit root_. Hal ini didapatkan dari nilai $p-value = 0.3913 > 0.05 = \alpha$.

Karena data tidak stasioner, maka akan dilakukan _differencing_. Plot data setelah dilakukan _differencing_ adalah sebagai berikut.

![After Differencing](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/b928fd1b-ab79-4b08-8910-9ce5ea846ea0)

Dari gambar tersebut dapat dilihat bahwa plot sudah tidak terlalu fluktiatif. Untuk memperkuat asumsi tersebut akan dilakukan uji ADF untuk data yang telah dilakukan _differencing_.

```
        Augmented Dickey-Fuller Test

data:  diff_rh
Dickey-Fuller = -4.1936, Lag order = 3, p-value = 0.0101
alternative hypothesis: stationary
```

Dari hasil tersebut, dapat disimpulkan bahwa pada tingkat signifikansi 0.05, data stasioner atau tidak memiliki _unit root_. Hal ini didapatkan dari nilai $p-value = 0.0101 < 0.05 = \alpha$.

## Spesifikasi Model
![ACF](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/98943dff-0e54-411c-92c7-5ed14883bc1e)

Dari plot ACF dan PACF masih tidak dapat ditentukan model ARIMA yang cocok untuk data. Maka, akan digunakan metode EACF engan indikasi model awal adalah ARIMA(p,1,q) karena data telah melalui differencing satu kali.

![EACF](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/ef68ff13-4f2e-4590-8761-4d48fd38adab)

Berdasarkan tabel EACF, model yang dapat diajukan adalah model 1: ARIMA(0,1,1) ; model 2: ARIMA(1,1,0) ; model 3: ARIMA(2,1,2) ; dan model 4: ARIMA(3,1,2).

Untuk memilih model terbaik akan dilakukan dengan membandingkan nilai AIC, BIC dan _likelihood_ model dengan kriteria model terbaik adalah nilai AIC dan BIC yang terkecil dan nilai _likelihood_ yang terbesar.

```
            modelrh1      modelrh2      modelrh3      modelrh4
coef        Numeric,2     Numeric,2     Numeric,5     Numeric,6
sigma2      26.96662      28.45212      22.68001      23.14855
var.coef    Numeric,4     Numeric,4     Numeric,25    Numeric,36
mask        Logical,2     Logical,2     Logical,5     Logical,6
loglik      -140.3082     -141.3648     -136.5121     -136.4466
aic         286.6164      288.7295      285.0241      286.8933
arma        Integer,7     Integer,7     Integer,7     Integer,7
residuals   Numeric,47    Numeric,47    Numeric,47    Numeric,47
call        Expression    Expression    Expression    Expression
series      "rhavg"       "rhavg"       "rhavg"       "rhavg"
code        0             0             0             0
n.cond      0             0             0             0
nobs        46            46            46            46
model       List,10       List,10       List,10       List,10
aicc        287.1879      289.301       287.178       289.8406
bic         292.1024      294.2155      295.996       299.6938
xreg        Integer,47    Integer,47    Integer,47    Integer,47
x           Numeric,47    Numeric,47    Numeric,47    Numeric,47
fitted      Numeric,47    Numeric,47    Numeric,47    Numeric,47
```

Dari tabel perbandingan model diatas dan kriteria pemilihan melalui AIC, BIC,
dan _log likelihood_ maka model yang terbaik adalah model 3 yaitu ARIMA(2,1,2).

## Estimasi Parameter
Metode estimasi parameter yang digunakan adalah metode _maximum likelihood_. Hasil estimasi parameter yang didapatkan adalah sebagai berikut.

```
Series: rhavg
ARIMA(2,1,2) with drift

Coefficients:
          ar1     ar2      ma1      ma2   drift
      -0.4710  0.5262  -0.0288  -0.9703  0.0138
s.e.   0.1366  0.1337   0.1478   0.1456  0.0946

sigma^2 estimated as 22.68: log-likelihood=-136.51
AIC=285.02   AICc=287.18   BIC=296
```

Dari _output_ tersebut didapatkan estimasi parameter untuk model ARIMA(2,1,2) adalah $\phi_1 = -0.4710$, $\phi_2 = 0.5262$, $\theta_1 = -0.0288$ dan $\theta_2 = -0.9703$. Sehingga model ARIMA(2,1,2) dapat dituliskan sebagai berikut.

$Y_t = 0.0138 + 0.592Y_{t-1} + 0.9972Y_{t-2} -0.5262Y_{t-3} + a_t + 0.0288a_{t-1} + 0.9703a_{t-2}$

## Diagnosa Model
Pada bagian ini akan dilakukan analisis residual dan _overfitting_.

### Analisis Residual
Pada tahap ini akan diuji apakah residual berdistribusi normal, independen dan merupakan _white noise_. 

+ Independensi/Autokorelasi Residual

Plot residual untuk model ARIMA(2,1,2) diberikan sebagai berikut

![Plot Residual](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/1c490202-03ec-4f96-869b-bfda906b5ef3)

Berdasarkan plot diatas, secara subjektif dapat dilihat bahwa tidak terdapat autokorelasi pada tiap residual. Selanjutnya akan dilakukan uji Ljung-Box untuk menguji independensi pada residual

```
        Ljung-Box Test

data:  Residuals from ARIMA(2,1,2) with drift
Q* = 7.4317, df = 4, p-value = 0.1148

Model df: 5. Total lags used: 9
```

Dari hasil uji Ljung-Box dapat disimpulkan bahwa **residual independen** karena nilai $p-value = 0.1148 > 0.05 = \alpha$.

```
        Augmented Dickey-Fuller Test

data:  rhfit$residuals
Dickey-Fuller = -3.6127, Lag order = 3, p-value = 0.04215
alternative hypothesis: stationary
```

Berdasarkan hasil uji ADF, nilai $p-value = 0.04215 < 0.05 = \alpha$ sehingga dapat disimpulkan bahwa **residual pada data stasioner**.

+ Normalitas Residual

Pada Normal Q-Q Plot untuk residual dibawah ini dapat dilihat bahwa residual tidak terlalu jauh dari garis diagonal pada plot. Dalam kata lain, secara subjektif data dinilai berdistribusi normal.

![Normal QQ Plot](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/7a9b3a6c-f5a0-49f1-96ae-171ffff6c89e)

Akan dilakukan uji Jarque-Bera untuk melihat apakah residual berdistribusi normal.

```
        Jarque-Bera test for normality

data:  rhfit$residuals
JB = 2.0841, p-value = 0.2053
```

Berdasarkan hasil uji Jarque-Bera, &p-value = 0.2053 > 0.05 = \alpha& sehingga dapat disimpulkan bahwa residual berdistribusi normal.

### _Overfitting_ Model
Overfitting dilakukan untuk memeriksa apakah model yang didapatkan sudah paling baik atau masih terdapat model yang lebih mendekati model sebenarnya atau model yang lebih baik daripada model ajuan awal.

Model yang akan diajukan adalah ARIMA (3,1,2) dan ARIMA (2,1,3). Akan dilakukan uji-t pada masing-masing parameter untuk melihat apakah parameter tersebut berbeda signifikan dengan nol atau tidak.

+ **Model ARIMA(3,1,2)**

```
Series: rhavg
ARIMA(3,1,2) with drift

Coefficients:
          ar1     ar2     ar3      ma1      ma2   drift
      -0.5025  0.5529  0.0567  -0.0192  -0.9807  0.0132
s.e.   0.1589  0.1550  0.1570   0.1519   0.1510  0.0989

sigma^2 estimated as 23.15: log-likelihood=-136.45
AIC=286.89   AICc=289.84   BIC=299.69
```

Maka statistik uji-t nya adalah:

![tARIMA312](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/7e3b7f32-6c7e-4303-abf3-b2d51de88baf)

Berdasarkan nilai t tersebut, didapatkan nilai $p-value = 1.280267$, sehingga dapat disimpulkan bahwa $H_0$ gagal ditolak sehingga parameter dikatakan tidak berbeda signifikan dengan nol atau model tidak berguna.

+ **Model ARIMA(2,1,3)**

```
Series: rhavg
ARIMA(3,1,2) with drift

Coefficients:
          ar1     ar2      ma1      ma2    ma3   drift
      -0.3946  0.6036  -0.1282  -0.9758  0.104  0.0129
s.e.   0.2381  0.2391   0.3079   0.1507  0.287  0.0995

sigma^2 estimated as 23.18: log-likelihood=-136.45
AIC=286.89   AICc=289.84   BIC=299.69
```

Maka statistik uji-t nya adalah:

![tARIMA213](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/46cb73fd-d0bb-445e-85bf-7d1b38c31747)

Berdasarkan nilai t tersebut, didapatkan nilai $p-value = 1.281304$, sehingga dapat disimpulkan bahwa $H_0$ gagal ditolak sehingga parameter dikatakan tidak berbeda signifikan dengan nol atau model tidak berguna.

+ **Pemilihan Model Terbaik**

Untuk memilih model terbaik, nilai AIC, BIC dan _log likelihood_ dari model ARIMA(2,1,2), ARIMA(3,1,2) dan ARIMA(2,1,3) akan dibandingkan.

```
            rhfit1        rhoverfit1    rhoverfit2
coef        Numeric,5     Numeric,6     Numeric,6
sigma2      22.68001      23.14855      23.18419
var.coef    Numeric,25    Numeric,36    Numeric,36
mask        Logical,5     Logical,6     Logical,6
loglik      -136.5121     -136.4466     -136.4464
aic         285.0241      286.8933      286.8928
arma        Integer,7     Integer,7     Integer,7
residuals   Numeric,47    Numeric,47    Numeric,47
call        Expression    Expression    Expression
series      "rhavg"       "rhavg"       "rhavg"
code        0             0             0      
n.cond      0             0             0      
nobs        46            46            46     
model       List,10       List,10       List,10
aicc        287.178       289.8406      289.8402
bic         295.996       299.6938      299.6933
xreg        Integer,47    Integer,47    Integer,47
x           Numeric,47    Numeric,47    Numeric,47
fitted      Numeric,47    Numeric,47    Numeric,47
```

Dari hasil diatas dapat disimpulkan bahwa model ARIMA (2,1,2) merupakan model terbaik untuk data rata-rata tingkat kelembapan udara DKI Jakarta. Hal ini dikarenakan model ARIMA(2,1,2) memiliki nilai AIC dan BIC yang terkecil serta nilai _log likelihood_ yang terbesar.

## Forecasting
Akan dilakukan _forecasting_ untuk 5 periode selanjutnya. Langkah pertama yang dilakukan adalah membagi data menjadi data testing dan data training. Untuk data kelembapan udara Provinsi DKI Jakarta, akan digunakan 5 data terakhir sebagai data _testing_ (data penguji) sedangkan data lainnya digunakan untuk data _training_ (data pembangun).

![forecastcv](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/7c95e041-56eb-4a02-9190-91ec863af3b2)

Jika dibandingkan dengan nilai aktualnya, maka didapatkan tabel dan plot sebagai berikut:

![hasilforecast](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/645513bf-ff8e-4d8e-9177-e16ccd383282)

![plottrain](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/bf9f8d9a-c7be-4f5a-a3ac-bfcf092d6bfc)

Dapat dilihat dari tabel perbandingan dan plot diatas bahwa data aktual (digambarkan dengan garis merah) masih berada pada interval kepercayaan 95% dari forecast yang dilakukan. Sehingga dapat disimpulkan bahwa model yang digunakan sudah baik untuk proses forecasting lima periode selanjutnya.

Selanjutnya, akan dilakukan peramalan tingkat kelembapan udara untuk 5 periode mendatang berdasarkan data rata-rata tingkat kelembapan udara (dalam %) Provinsi DKI Jakarta sejak tanggal 1 November 2021 hingga 17 Desember 2021.

![hasilforecastreal](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/be5413fb-c653-4d87-b454-a57f1b036aec)

![Screen Shot 2023-07-31 at 21 27 29](https://github.com/hunaiva-kintan/Time-Series-Project/assets/99937211/084e2087-537a-4c26-ac4b-446a59711813)

## Kesimpulan
Pada tanggal 18 Desember rata-rata tingkat kelembapan udara Provinsi DKI Jakarta diprediksi sebesar 76.40479%. Untuk tanggal 19 Desember 2021 diprediksi rata-rata tingkat kelembapannya adalah sebesar 78.71824%. Pada tanggal 20, 21, dan 22 Desember 2021 secara berurutan diprediksi rata-rata tingkat kelembapan udara Provinsi DKI Jakarta adalah sebesar 76.27592%, 78.65666%, dan 76.26310%.

## Saran
1. Masyarakat lebih memerhatikan pergerakan perubahan iklim (kelembapan dan
temperatur udara) sehingga dapat menentukan langkah terbaik apa yang harus
dilakukan.
2. Pemerintah mampu mengupayakan dan mensosialisasikan langkah terbaik selanjutnya
yang harus dilakukan masyarakat guna menjaga kestabilan iklim.
3. Peneliti selanjutnya mampu menggunakan data dengan rentang waktu yang lebih panjang dan memuat informasi yang lebih detail sehingga dapat menunjukkan hasil
yang lebih akurat.
4. Peneliti selanjutnya mampu mempertimbangkan dan memperhitungkan berbagai
faktor lain yang mampu mempengaruhi grafik pergerakan perubahan iklim (kelembapan dan temperatur udara).
