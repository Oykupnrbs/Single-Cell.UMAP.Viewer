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
