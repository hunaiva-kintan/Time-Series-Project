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
