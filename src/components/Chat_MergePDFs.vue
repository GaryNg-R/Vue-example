<template>
  <div>
    <h1>Merge PDFs</h1>
    <input type="file" multiple accept=".pdf" @change="handleFiles" />
    <button @click="mergePDFs">Merge PDFs</button>
  </div>
</template>

<script>
import PDFNet from '@pdftron/pdfnet-node';
import fs from 'fs';
import path from 'path';

export default {
  data() {
    return {
      pdfFiles: [],
    };
  },
  methods: {
    handleFiles(event) {
      this.pdfFiles = Array.from(event.target.files);
    },
    async mergePDFs() {
      if (this.pdfFiles.length === 0) {
        alert('Please select PDF files to merge.');
        return;
      }

      try {
        await PDFNet.runWithCleanup(async () => {
          const outputDoc = await PDFNet.PDFDoc.create();
          outputDoc.initSecurityHandler();

          for (const file of this.pdfFiles) {
            const filePath = path.resolve(file.path); // Get the full path of the file
            const pdfDoc = await PDFNet.PDFDoc.createFromFilePath(filePath);
            pdfDoc.initSecurityHandler();
            const pageCount = await pdfDoc.getPageCount();

            for (let i = 1; i <= pageCount; i++) {
              const page = await pdfDoc.getPage(i);
              outputDoc.pagePushBack(page);
            }
          }

          const outputPath = path.join(__dirname, 'merged.pdf');
          await outputDoc.save(outputPath, PDFNet.SDFDoc.SaveOptions.e_linearized);
          alert('PDFs merged successfully!');

          // Optional: Open merged PDF in a new tab
          const data = new Uint8Array(fs.readFileSync(outputPath));
          const blob = new Blob([data], { type: 'application/pdf' });
          const url = window.URL.createObjectURL(blob);
          window.open(url);
        });
      } catch (error) {
        console.error('Error merging PDFs:', error);
      }
    },
  },
};
</script>

<style scoped>
/* Add your styles here */
</style>
