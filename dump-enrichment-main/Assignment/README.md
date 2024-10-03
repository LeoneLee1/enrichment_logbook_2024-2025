## Get ALL Assignment Instanly
meskipun due date lewat / masih minta request extend, ini tetap work dek

1. Buka enrichment app
2. Pilih tab "Assignment"
3. Copy Kode dibawah ini, lalu paste pada inspect element -> console
```javascript
var xhr = new XMLHttpRequest();
xhr.open("POST", "https://activity-enrichment.apps.binus.ac.id/Assignment/GetAssignment", true);
xhr.setRequestHeader("Content-Type", "application/json;charset=UTF-8");

xhr.onreadystatechange = function() {
    if (xhr.readyState === XMLHttpRequest.DONE && xhr.status === 200) {
        var response = JSON.parse(xhr.responseText);
        var assignments = response.data;

        assignments.forEach(function(assignment) {
            var fileXhr = new XMLHttpRequest();
            fileXhr.open("GET", assignment.assignmentFilePath, true);
            fileXhr.responseType = "blob";

            fileXhr.onload = function() {
                if (fileXhr.status === 200) {
                    var url = window.URL.createObjectURL(fileXhr.response);
                    var a = document.createElement("a");
                    a.style.display = "none";
                    a.href = url;
                    a.download = assignment.assignmentFilePathRaw;
                    document.body.appendChild(a);
                    a.click();
                    window.URL.revokeObjectURL(url);
                }
            };

            fileXhr.send();
        });
    }
};

xhr.send(null);
```
