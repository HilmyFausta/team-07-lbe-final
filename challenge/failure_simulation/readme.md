# Simulasi Kegagalan VM

awalnya terdapat 4 VM dan semuanya healthy
![lbhealthy](../../docs/foto/load_balancer_healthy.png)


setelah disimulasikan 1VM mati
![matiin](../../docs/foto/mematikan_vm.png)

hasil curl setelah vm dimatikan
![bukticurlmati](../../docs/foto/bukti_curl_mati.png)

hasil insight load balancer
![lbgaksehat](../../docs/foto/unhealthy.png)

setelah vm kembali dinyalakan
![vmnyala](../../docs/foto/menyalakan_vm.png)

curl kembali normal 4 user
![curl_sehat](../../docs/foto/bukti_curl_nyala.png)

insight kembali healthy
![awal_sehat](../../docs/foto/healthy.png)