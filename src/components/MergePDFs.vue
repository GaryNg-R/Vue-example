<template>
  <div>
    <h1>Merge PDFs</h1>
    <button @click="mergeDocuments">Merge PDFs</button>
  </div>
</template>

<script>
import { Core } from '@pdftron/webviewer';

export default  {
  mounted() {
    this.initializePDFTron();
  },
  methods: {
    async initializePDFTron() {
      try {
        // Initialize PDFTron Core
       //await Core.start();

        // Set backend type to EMS
        Core.forceBackendType('ems');

        // Set path to PDF worker
        Core.setPDFWorkerPath('/lib/core/pdf');

        // Disable embedded JavaScript
        Core.disableEmbeddedJavaScript();

        console.log('PDFTron Core initialized successfully.');
      } catch (error) {
        console.error('Error initializing PDFTron Core:', error);
      }
    },
  async mergeDocuments() {
  try {
    // Array of URLs of PDFs to merge
    const urls = [
      'https://pdftron.s3.amazonaws.com/downloads/pl/demo-annotated.pdf',
      'https://pdftron.s3.amazonaws.com/downloads/pl/Cheetahs.pdf',
      'https://pdftron.s3.amazonaws.com/downloads/pl/magazine-short.pdf',
    ];

    // Merge documents recursively
    const mergedPdf = await this.mergeDocuments(urls);
    console.log('Merged PDF:', mergedPdf);

    // Optionally download merged PDF
    const blob = new Blob([mergedPdf.getFileData()], { type: 'application/pdf' });
    const url = window.URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.setAttribute('download', 'merged.pdf');
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  } catch (error) {
    console.error('Error merging PDFs:', error);
  }
},
async mergeDocuments(urlArray, nextCount = 1, doc = null) {
  return new Promise(async function(resolve, reject) {
    if (!doc) {
      doc = await Core.createDocument(urlArray[0]);
    }

    const newDoc = await Core.createDocument(urlArray[nextCount]);
    const newDocPageCount = await newDoc.getPageCount();

    const pages = Array.from({ length: newDocPageCount }, (v, k) => k + 1);
    const pageIndexToInsert = await doc.getPageCount() + 1;

    await doc.insertPages(newDoc, pages, pageIndexToInsert);
    resolve({
      next: urlArray.length - 1 > nextCount,
      doc: doc,
    });
  }).then(res => {
    return res.next ?
      this.mergeDocuments(urlArray, nextCount + 1, res.doc) :
      res.doc;
  });
}
  },
};

async function mergeDocuments(urlArray, nextCount = 1, doc = null) {
  return new Promise(async function(resolve, reject) {
    if (!doc) {
      doc = await Core.createDocument(urlArray[0]);
    }

    const newDoc = await Core.createDocument(urlArray[nextCount]);
    const newDocPageCount = await newDoc.getPageCount();

    const pages = Array.from({ length: newDocPageCount }, (v, k) => k + 1);
    const pageIndexToInsert = await doc.getPageCount() + 1;

    await doc.insertPages(newDoc, pages, pageIndexToInsert);
    resolve({
      next: urlArray.length - 1 > nextCount,
      doc: doc,
    });
  }).then(res => {
    return res.next ?
      mergeDocuments(urlArray, nextCount + 1, res.doc) :
      res.doc;
  });
}
</script>

<style scoped>
/* Add your styles here */
</style>
