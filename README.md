# Pusula_Zeynep_Bolukbasi
Data Science Intern Case Study

Name:Zeynep 

Surname: Bolukbasi

Email: zeynep.bolukbasi@gmail.com

Treatment Duration Prediction (EDA, Pipeline, and Model Report)

1) Data Overview
I worked with a clinical dataset of 2,235 rows and 13 original columns. The first peek confirms the shape (2235, 13) and shows the raw header as: HastaNo, Yas, Cinsiyet, KanGrubu, Uyruk, KronikHastalik, Bolum, Alerji, Tanilar, TedaviAdi, TedaviSuresi, UygulamaYerleri, UygulamaSuresi.I also inspected types and non-null counts using data.info() during the workflow, as columns were added/dropped over steps.

2) Missingness & Basic Distributions
I visualized missing values with missingno and a NaN-ratio bar chart; importantly, no column exceeded 60% missingness (the list of “>60% NaN” columns was empty). I also plotted histograms for numeric columns to see their distributions. I dropped the pure ID field HastaNo early, since it carries no predictive signal.

3) Feature-by-Feature EDA & Cleaning (Original 13)
Below I summarize exactly how I explored and processed each of the 13 original features.

3.1 Yas (Age)
Numeric variable used downstream. It appears later among continuous features in the modeling block.

3.2 Cinsiyet (Gender)
Raw categories were Kadın, Erkek, and NaN with counts (Kadın 1274, Erkek 792, NaN 169). I mapped to {Kadın:0, Erkek:1}, then imputed missing with the mode, moved the cleaned integer back to Cinsiyet, and dropped the temporary column. I also visualized its distribution (pie).

3.3 KanGrubu (Blood Type)
Initial distribution included 675 NaNs across nine labels. I filled missing values with the most frequent blood type and applied one-hot encoding to obtain eight KanGrubu columns. I then dropped the original KanGrubu and visualized.

3.4 Uyruk (Nationality)
I grouped rare categories (<50) under “Diğer”, created Uyruk_mod, and one-hot encoded it into Uyruk_Türkiye and Uyruk_Diğer (explicitly cast to integers for consistency).

3.5 KronikHastalik (Chronic Disease)
I filled missing with "HastalıkYok", split multi-label strings (,), counted frequencies, identified the top 15 diseases, and then created binary flags for the top 10 (e.g., Hastalik_Aritmi, etc.). Finally, I dropped the original text column.

3.6 Bolum (Department)
I collapsed this into a binary indicator Bolum_FizikTedavi for the dominant department “Fiziksel Tıp Ve Rehabilitasyon, Solunum Merkezi,” then dropped the original field and checked frequencies (2045 vs. 190). A pie chart was also produced.

3.7 Alerji (Allergy)
I imputed missing with “AlerjiYok”, upper-cased/trimmed, and corrected common typos (e.g., Nova1gin→NOVALGIN, Volteren→VOLTAREN, etc.). Then I computed overall frequencies, selected the top 8 allergens, created binary flags (e.g., Alerji_POLEN, Alerji_NOVALGIN, …), 
added a Alerji_Diger indicator, and computed Toplam_Alerji_Sayisi. Finally, I dropped the original text column and plotted the top allergens.

3.8 Tanilar (Diagnoses)
I explored unique values and counts; later, I created diagnosis group features (e.g., Tani_Grup_KAS_ISKELET, etc.) from available Tani_* fields, visualized the top 10 diagnoses, and dropped Tanilar (and Ana_Tanilar) once derived features existed.

3.9 TedaviAdi (Treatment Name)
This text field remains in the raw structure; in modeling, I instead rely on aggregate textual features that show up later among continuous inputs (e.g., Tedavi_Uzunluk, Tedavi_Kelime_Sayisi).

3.10 TedaviSuresi (Sessions — Target Text)
I analyzed unique labels and counts (e.g., “15 Seans” dominating), then transformed it into a numeric target tedavi_suresi_numeric and validated its stats: mean 14.57, median 15.0, min 1, max 37 with no missing. Afterward, I dropped the original TedaviSuresi.

3.11 UygulamaYerleri (Application Site)
I engineered body-region flags (e.g., Bolge_AltEkstremite from lower-limb sub-regions) and then dropped the original text column.

3.12 UygulamaSuresi (Application Duration)
I extracted minutes from the string labels (e.g., “20 Dakika”) into UygulamaSuresi_Dk, then applied MinMaxScaler, creating UygulamaSuresi_Scaled, and dropped the intermediates. I also inspected the raw distribution before conversion.

3.13 HastaNo (Patient ID)
Dropped as an identifier.

5) Final Feature Set 
Later data.info() calls show the growing feature matrix (82–89 columns after derivations). Two key targets I use are UygulamaSuresi_Scaled and tedavi_suresi_numeric present and non-null at the end of preprocessing.

7) Modeling Pipeline
I framed tedavi_suresi_numeric as the regression target and placed only the truly continuous columns under StandardScaler using a ColumnTransformer; all binary columns passed through unchanged. The estimator is a RandomForestRegressor in a scikit-learn Pipeline. I split the data 80/20 for train/test.

Continuous features used in the scaler: ['Yas', 'Toplam_Alerji_Sayisi', 'Toplam_Tani_Sayisi', 'Toplam_Tedavi_Kategori', 'Tedavi_Uzunluk', 'Tedavi_Kelime_Sayisi', 'UygulamaSuresi_Scaled']. Binary features include Cinsiyet, KanGrubu, Uyruk_Diğer, and many others (total ~74).

9) Results
On the held-out test set, the model achieves:
R² = 0.9390
MSE = 0.9348
These results indicate the pipeline generalizes well to unseen data under the chosen split and feature space.

