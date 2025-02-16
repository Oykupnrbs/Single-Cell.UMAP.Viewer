import os
import zipfile
import scanpy as sc
import matplotlib.pyplot as plt
from shiny import App, ui, render

# Shiny arayüzü
def app_ui(request):
    return ui.page_fluid(
        ui.h2("Interactive Single-Cell UMAP Viewer"),
        ui.input_file("file_upload", "Upload ZIP File", multiple=False, accept=[".zip"]),
        ui.output_plot("umap_plot"),
        ui.output_text("error_message")
    )

# Sunucu fonksiyonları
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
