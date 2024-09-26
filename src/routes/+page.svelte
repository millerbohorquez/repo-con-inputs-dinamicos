<script>
    import * as XLSX from 'xlsx';
  
    let file; 
    let sheetsData = {}; 
    let selectedSheet = ''; 
    let sheetNames = []; 
  

    
    function handleFile(event) {
      const selectedFile = event.target.files[0];
      const reader = new FileReader();
  
      reader.onload = function (e) {
        const binaryString = e.target.result;
        const workbook = XLSX.read(binaryString, { type: 'binary' });
  
        
        sheetsData = {};
        sheetNames = workbook.SheetNames;
  
        
        sheetNames.forEach(sheetName => {
          const sheet = XLSX.utils.sheet_to_json(workbook.Sheets[sheetName], { header: 1 }); 
          sheetsData[sheetName] = sheet; 
        });
  
        
        if (sheetNames.length > 0) {
          selectedSheet = sheetNames[0];
        }
      };
  
      reader.readAsBinaryString(selectedFile);
    }
  </script>
  
 
  
  <div>
    <h2>Cargar archivo en Excel para generar inputs dinámicos</h2>
    <input type="file" accept=".xlsx, .xls" on:change={handleFile} />
  
    {#if sheetNames.length > 0}
      <h3>Selecciona una hoja:</h3>
      <select bind:value={selectedSheet}>
        {#each sheetNames as sheetName}
          <option value={sheetName}>{sheetName}</option>
        {/each}
      </select>
  
      <h3>Inputs generados desde la hoja "{selectedSheet}":</h3>
      {#if sheetsData[selectedSheet]}
        {#each sheetsData[selectedSheet] as row, rowIndex}
          <div class="input-container">
            {#each row as cell, cellIndex}
              <label>{`Campo ${cellIndex + 1}: `}</label>
              <input type="text" value={cell || ''} />
            {/each}
          </div>
        {/each}
      {/if}
    {/if}
  </div>
  

  <style>
    
    .input-container {
      margin: 10px 0;
    }
  </style>