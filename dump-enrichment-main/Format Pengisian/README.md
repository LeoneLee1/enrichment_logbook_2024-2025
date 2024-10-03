## Catatan Penting :
Jika sudah kalian isi semua, pastikan menghapus baris yang terdapat `-`, karena pada baris itu saya set bahwa hari tersebut adalah libur (minggu) secara default di website enrichment.

Contoh :
```
Dari :
----------------------------------------------------
2024-06-01|CLOCK_IN|CLOCK_OUT|ACTIVIY|DESCRIPTION
-
2024-06-03|CLOCK_IN|CLOCK_OUT|ACTIVIY|DESCRIPTION

Menjadi :
----------------------------------------------------
2024-06-01|CLOCK_IN|CLOCK_OUT|ACTIVIY|DESCRIPTION
2024-06-03|CLOCK_IN|CLOCK_OUT|ACTIVIY|DESCRIPTION
```

## Penjelasan
|Variabel|Penjelasan|
|---|---|
|`CLOCK_IN`|ganti dengan `jam "am/pm"` / set `OFF` jika kalian libur / ada keperluan lain, contoh: `09:30 am`|
|`CLOCK_OUT`|ganti dengan `jam "am/pm"` / set `OFF` jika kalian libur / ada keperluan lain, contoh: `05:00 pm`|
|`ACTIVITY`|ganti dengan `aktifitas kalian`, contoh: `Membuat sarapan`|
|`DESCRIPTION`|ganti dengan `deskripsi kalian`, contoh: `Supaya kenyang`|

## Contoh Pengisian :
- Jika tidak libur
`2024-06-01|09:30 am|05:00 pm|Belajar|Belajar dengan menonton youtube`

Contoh tampilan diatas, jika diupload ke enrichment app :
|DATE|CLOCK IN|CLOCK OUT|ACTIVITY|DESCRIPTION|
|---|---|---|---|---|
|2024-06-01|09:30 am|05:00 pm|Belajar|Belajar dengan menonton youtube|

- Jika libur / ada keperluan lain (tanpa aktifitas / deskripsi)
`2024-06-01|OFF|OFF|OFF|OFF`

Contoh tampilan diatas, jika diupload ke enrichment app :
|DATE|CLOCK IN|CLOCK OUT|ACTIVITY|DESCRIPTION|
|---|---|---|---|---|
|2024-06-01|OFF|OFF|OFF|OFF|

- Jika libur / ada keperluan lain (dengan aktifitas / deskripsi)
`2024-06-01|OFF|OFF|Sakit|Sakit demam tinggi`

|DATE|CLOCK IN|CLOCK OUT|ACTIVITY|DESCRIPTION|
|---|---|---|---|---|
|2024-06-01|OFF|OFF|Sakit|Sakit demam tinggi|

## Direct download template
```javascript
javascript:(function(){
  const logBookHeaderID = document.querySelector('ul[id="monthTab"] li.current a').getAttribute('onclick').split("'")[1];
  const url = 'https://activity-enrichment.apps.binus.ac.id/LogBook/GetLogBook';
  const postData = new URLSearchParams();
  postData.append('logBookHeaderID', logBookHeaderID);

  fetch(url, {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8' },
    body: postData.toString()
  })
  .then(response => response.json())
  .then(result => {
    const formattedData = result.data
      .map(entry => {
        const date = new Date(entry.date.split('T')[0]);
        const dayOfWeek = date.getDay();

        if (dayOfWeek === 0) {
          return "-";
        } else if (entry.clockIn === 'OFF' && entry.clockOut === 'OFF') {
          return "-";
        } else {
          return `${entry.date.split('T')[0]}|${entry.clockIn || 'CLOCK_IN'}|${entry.clockOut || 'CLOCK_OUT'}|${entry.activity || 'ACTIVITY'}|${entry.description || 'DESCRIPTION'}`;
        }
      })
      .join('\n');

    const monthName = document.querySelector('ul[id="monthTab"] li.current a').text.split(' ')[0].trim().toLowerCase();
    const fileName = `logbook-${monthName}.txt`;

    const blob = new Blob([formattedData], { type: "text/plain" });
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = fileName;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  })
  .catch(error => console.error('Error fetching logbook data:', error));
})();
```
