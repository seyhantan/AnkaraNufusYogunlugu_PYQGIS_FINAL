# AnkaraNufusYogunlugu_PYQGIS_FINAL
QGIS ve Python kullanılarak Ankara mahallelerinin nüfus verileri doğrultusunda hazırlanan nüfus yoğunluğu haritası


Daha önce derste verilen Ankara Mahalleler shp dosyasına uygun olarak mahale nüfuslarını içeren csv uzantılı bir nüfus verisi hazırlandı. .shp ve .csv dosyalarında ortak kolonlar; python yazılım dilinin qgis kütüphaneleri kullanılarak QgsVectorLayerJoinInfo metodu yardımıyla birleştirildi. Birleştirilmiş veriler üzerinden nufüs verileri aralıklarına göre python ile yoğunluk haritası oluşturuldu. Bu analiz, Ankara nüfus yoğunluklarının mahallelere dağılım verisini haritalandırılmış bir biçimde görselleştirmeyi amaçlamıştır.


![AnkaraNufusHarita](https://github.com/seyhantan/AnkaraNufusYogunlugu_PYQGIS_FINAL/assets/148454109/acfb7049-c75f-4768-b870-fe69ff49fa26)



İlgili Python Komutları

from qgis.core import QgsProject, QgsVectorLayer, QgsCategorizedSymbolRenderer, QgsSymbol, QgsRendererCategory

# Veri setlerinin dosya yollarını belirtin
shp_path = "C:/Users/seyha/OneDrive/Masaüstü/PYTHON_H/workshop/ankara_mahalleler.shp"
csv_path = "C:/Users/seyha/OneDrive/Masaüstü/PYTHON_H/workshop/PopDensDene1me.csv"

# Şekil dosyasını yükleyin
shp_layer = QgsVectorLayer(shp_path, "ankara_mahalleler.shp", "ogr")


# CSV dosyasını yükleyin
csv_layer = QgsVectorLayer(f'file://{csv_path}?delimiter=,', 'PopDensDene1me.csv', 'delimitedtext')

# Veri katmanlarını QGIS projesine ekleyin
QgsProject.instance().addMapLayer(shp_layer)
QgsProject.instance().addMapLayer(csv_layer)

# İlgili alan adlarını belirtin (OBJECTID ve nufus gibi)
shp_field = "NAME"
csv_field = "nufus"

# İki katmanı birleştirin
join_info = QgsVectorLayerJoinInfo()
join_info.setJoinFieldName(shp_field)
join_info.setTargetFieldName(shp_field)
join_info.setJoinLayerId(shp_layer.id())
join_info.setUsingMemoryCache(True)
shp_layer.addJoin(join_info)

# Renklendirme için uygun semboller ve kategorileri belirtin
symbol = QgsSymbol.defaultSymbol(shp_layer.geometryType())
renderer = QgsCategorizedSymbolRenderer(csv_field, [])

# Kategorileri tanımlayın ve renkleri belirtin
categories = [
    QgsRendererCategory(0, symbol, '0-295'),
    QgsRendererCategory(295, symbol, '295-800'),
    QgsRendererCategory(800, symbol, '800-2000'),
    QgsRendererCategory(2000, symbol, '2000-5000'),
    QgsRendererCategory(5000, symbol, '5000-10000'),
    QgsRendererCategory(10000, symbol, '10000-48822')
]

# Kategorileri ekleyin
renderer.setCategories(categories)

# Renklendiriciyi ayarlayın
shp_layer.setRenderer(renderer)

# QGIS projenizi güncelleyin
QgsProject.instance().reloadAllLayers()

# QGIS projesini kaydedin
QgsProject.instance().write()











Verilen Ödev kapsamında yapılmak istenilen projede kullanılması planlanan python kodları için ChatGpt kullanılmıştır.
Vize 2 ödevi için verilen Ankara Mahalleler .shp dosyasında öznitelik tablosunda yer alan NAME sütunu ile ortak sütunu olan .csv dosyası oluşturuldu.
Burada internetten alınan nüfus verileri kullanıldı.
.shp ve .csv dosyaları join komutu ile aynı sütun bilgisi üzerinden birleştirildi.

Qgiste yer alan python eklentisinde yukarıda yer alan kodlar çalıştırılmaya başlanıldığında;
İlk olarak; 
TypeError: QgsRendererCategory(): arguments did not match any overloaded call:
      overload 1: too many arguments
      overload 2: argument 5 has unexpected type 'bool'
      overload 3: argument 1 has unexpected type 'str' hatası alındı. 

bu hata ile ilgili çözüm olarak; 
  "QgsRendererCategory" kullanımı değiştirildi.

İkinci olarak;

File "<string>", line 35, in <module>
    TypeError: 'field' is an unknown keyword argument hatası alındı.
bu hata ile ilgili çözüm olarak;
QgsRendererCategory'nin yerine QgsCategoryRenderer sınıfı kullanıldı ve kodlar buna göre düzenlendi.

Üçüncü olarak;

TypeError: QgsRendererCategory(): arguments did not match any overloaded call:
  overload 1: too many arguments
  overload 2: argument 5 has unexpected type 'bool'
  overload 3: argument 1 has unexpected type 'str' hatası alındı.
QgsRendererCategory için gereken parametrelerinin tekrardan düzenlenerek kullanımı doğrultusunda kodlar yeniden düzenlendi 'setCategories' yöntemi kullanıldı ve çalıştırıldı.

Dördüncü olarak;

CSV dosyası yüklenemedi hatası ile birlikte; AttributeError: 'QgsCategorizedSymbolRenderer' object has no attribute 'setCategories' hatası alındı. 
çözüm önerisi olarak; kodlar aynı şekilde tekrardan yazdırılması önerildi.

Kodlar tekrar gözden geçirilerek yazıldığında;

Beşinci olarak;
Traceback (most recent call last):
  File "C:\PROGRA~1\QGIS33~1.1\apps\Python39\lib\code.py", line 90, in runcode
    exec(code, self.locals)
  File "<input>", line 1, in <module>
  File "<string>", line 48, in <module>
    AttributeError: 'QgsCategorizedSymbolRenderer' object has no attribute 'setCategories' hatası alındı.

Çözüm olarak; QgsRendererCategory kullanımının değişimi önerildi ve kodlar tekrar düzenlendi ve yine aynı hatalar alındı.

Bunlar doğrutulsunda yapılanlara ek olarak; yoğunluk haritası kısmında eksik kalan kısımlar için Qgis programı ile shp ve csv dosyalarının ortak sütunu ile sayısallaştırma yapıldı ve nüfus yoğunluğuna göre haritada renklendirme yapılarak; lejant eklenerek dosya kaydedildi.









