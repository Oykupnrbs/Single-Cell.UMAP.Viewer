# Single-Cell.UMAP.Viewer
Interactive Single-Cell UMAP Viewer with Shiny for Python

İlk olarak kullanacağım kütüphaneleri yazdım.

import os 
import zipfile
import scanpy as sc
import matplotlib.pyplot as plt 
from shiny import App, ui, render

Bunlar uygulama için gerekli kütüphaneler ve import ile onları içe aktarıyoruz.

Os kütüphanesi, dosya ve klasör işlemleri için kullanılır. (dosya yolu oluşturma, klasör açma, vb.)

Zipfile kütüphanesi, ZIP dosyalarını açmak, sıkıştırmak ve üzerinde işlem yapmak için kullanılır.

Scanpy kütüphanesi, veri analizi, görselleştirme ve hesaplamalar için bir dizi araç sağlar (örneğin, PCA, UMAP, gen ekspresyon analizi). Bu kütüphaneyi özellikle tek hücreli verilerle çalışmak için kullanırız.

4.satırda Matplotlib'in pylot modülünü içe aktarır. Bu modül, Python'da grafikler ve görselleştirmeler oluşturmak için kullanılır.

Shiny, Python'da etkileşimli web uygulamaları oluşturmak için kullanılır. App, uygulama için temel yapı taşlarını sağlar; ui, kullanıcı arayüzü bileşenlerini tanımlar ve render, çıktıları dinamik olarak oluşturmak için kullanılan fonksiyonları içerir.

İkinci olarak arayüzü kodladım. Bunun için bir fonksiyon oluşturdum.

def app_ui(request): 
    return ui.page_fluid(
        ui.h2("Interactive Single-Cell UMAP Viewer"),
        ui.input_file("file_upload", "Upload ZIP File", multiple=False, accept=[".zip"]),
        ui.output_plot("umap_plot"),
        ui.output_text("error_message")
    )
def app_ui(request): Bu fonksiyon, Shiny uygulamasının kullanıcı arayüzünü oluşturur.

return ui.page_fluid(): Bu satır, Shiny kullanıcı arayüzünün temel yapı taşını döndürür.

ui.page_fluid(): Bu fonksiyon, tüm sayfa düzenini birleştirir ve esnek bir düzen sunar.

ui.h2("Interactive Single-Cell UMAP Viewer"): Bu kod, bir başlık ekler.

ui.input_file("file_upload", "Upload ZIP File", multiple=False, accept=[".zip"]): Bu kod ile kullanıcının dosya yüklemesine izin verilir.

"file_upload": Bu kod, kullanıcı arayüzü ile sunucu fonksiyonları arasında veri iletimi için kullanılır.

"Upload ZIP File": Bu, yükleme alanının yanında görünen metindir.

multiple=False: Bu parametre, kullanıcının sadece bir dosya yüklemesine izin verir. 

accept=[".zip"]: Bu kod kullanıcının sadece ZIP dosyalarını yüklemesine izin verir.

ui.output_plot("umap_plot"): Bu kod, UMAP grafiğini gösterecek bir alan oluşturur.

ui.output_text("error_message"): Bu kod, hata mesajlarını gösterecek bir alan oluşturur.

Son olarak sunucu fonksiyonlarını yazdım.

def server(input, output):
    
    @output
    @render.text
    def error_message():
        return ""
    
    @output
    @render.plot
    def umap_plot():
        if not input.file_upload():
            print("Error: Please upload a ZIP file!")
            return None
        
        file_info = input.file_upload()[0]
        zip_path = file_info["datapath"]
        extract_dir = "extracted_files"
        os.makedirs(extract_dir, exist_ok=True)
        
        try:
            with zipfile.ZipFile(zip_path, 'r') as zip_ref:
                if not zip_ref.namelist(): 
                    return "ZIP file is empty. Please upload a valid ZIP file."
                zip_ref.extractall(extract_dir)
        except zipfile.BadZipFile:
            return "Invalid ZIP file. Please upload a valid .zip file."
        
        h5ad_files = [f for f in os.listdir(extract_dir) if f.endswith(".h5ad")]
        if not h5ad_files:
            return "No .h5ad file found in the ZIP."
        
def server(input, output): Sunucu fonksiyonu kullanıcı etkileşimlerine tepki verir.

input: Kullanıcının girdiği değerler.

output: Sunucudan kullanıcı arayüzüne gönderilecek değerler.

@output: Fonksiyonun bir çıktı fonksiyonu olduğunu belirtir.

@render.text: Fonksiyonun metin çıktısı döndürdüğünü belirtir.

def error_message(): Bu fonksiyon, kullanıcı arayüzünde hata mesajını gösterecek metni döndürür.

return "": Bu fonksiyon herhangi bir hata mesajı yoksa boş bir metin döndürüyor.

@output: Bu fonksiyonun arayüzüne bir çıktı göndereceğini belirtir.

@render.plot: Bu çıktı bir grafik olacağı için, Shiny'nin "plot" render'ı kullanılır.

def umap_plot(): Bu fonksiyon, UMAP görselleştirmesini oluşturmak için tanımlanmıştır.

if not input.file_upload(): Kullanıcının dosya yükleyip yüklemediğini kontrol eder. Eğer yüklememişse Shiny'nin boş bir grafik dödürmemesi için return None ile None gönderiyoruz. print("Error: Please upload a ZIP file!") kodu ile hata mesajı veriyor.

input.file_upload()[0]: Listenin 0.index’ini seçerek yüklenen dosyanın bilgilerini alır.

file_info["datapath"]: Shiny tarafından oluşturulan geçici dosya yolunu içerir.

extract_dir = "extracted_files": Zip içeriği buraya çıkartılacak, böylece dosyaları işleyebiliriz.

os.makedirs(extract_dir, exist_ok=True): Klasör yoksa oluşturur, exist_ok=True klasör zaten varsa hata vermemesi için kullanılıyor.

try: Eğer bir hata oluşursa program çökmez onun yerine except bloğuna geçer ve hata mesajı döndürülür.

with zipfile.ZipFile(zip_path, 'r') as zip_ref: with dosyanın işlem bittikten sonra otomatik olarak kapatılmasını sağlar, zipfile.ZipFile() zip dosyasını açmak için kullanılıyor, zip_path kullanıcının yüklediği zip dosyasının geçici dosya yolunu içerir, 'r' modu zip dosyasını okuma modunda açtığımızı belirtir, açtığımız dosyayı da zip_ref adlı zip dosyasını temsil eden nesne içinde saklıyoruz.

if not zip_ref.namelist(): Eğer ZIP dosyası tamamen boşsa return "ZIP file is empty. Please upload a valid ZIP file." koduyla zip dosyası boş mesajı döndürülür.

zip_ref.extractall(extract_dir): Zip içindeki tüm dosyaları extract_dir klasörüne aktarır.

except zipfile.BadZipFile: Eğer dosya zip formatında değilse hata oluşur ve return "Invalid ZIP file. Please upload a valid .zip file." koduyla bu hata yazısı karşımıza çıkar.

h5ad_files = [f for f in os.listdir(extract_dir) if f.endswith(".h5ad")]: Bu satır sadece .h5ad uzantılı dosyaları içeren bir liste oluşturur.

if not h5ad_files: Eğer h5ad_files listesi boşsa return "No .h5ad file found in the ZIP." koduyla hata mesajı döndürülür.
