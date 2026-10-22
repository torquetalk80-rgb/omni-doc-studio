<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>OmniDoc - Readable Passages & PDF Converter</title>
  
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- jsPDF Library (for PDF generation) -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <!-- PDF.js Library (for fast client-side PDF reading) -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
  
  <style>
    .dragover {
      border-color: #3b82f6 !important;
      background-color: #eff6ff !important;
    }
  </style>
</head>
<body class="bg-slate-100 text-slate-800 font-sans min-h-screen flex flex-col items-center justify-between p-3 sm:p-6">

  <!-- Header -->
  <header class="w-full max-w-4xl bg-white shadow-sm rounded-xl p-4 sm:p-5 mb-5 border border-slate-200 text-center">
    <div class="inline-flex items-center gap-2 mb-1">
      <span class="w-2.5 h-2.5 rounded-full bg-emerald-500 animate-pulse"></span>
      <h1 class="text-2xl sm:text-3xl font-extrabold text-slate-900 tracking-tight">OmniDoc Studio</h1>
    </div>
    <p class="text-xs sm:text-sm text-slate-500">Transform messy line-by-line PDFs into easy-to-read passages & convert heavy images (30MB+ supported).</p>
    
    <!-- Ad Placeholder -->
    <div class="mt-3 p-2 bg-slate-50 border border-dashed border-slate-200 text-[11px] text-slate-400 rounded">
      [AdSense Banner Area - 728x90]
    </div>
  </header>

  <!-- Main Utility Card -->
  <main class="w-full max-w-3xl bg-white shadow-md rounded-2xl p-5 sm:p-7 border border-slate-200">
    
    <!-- Mode Switcher Tabs -->
    <div class="grid grid-cols-2 gap-2 bg-slate-100 p-1.5 rounded-xl mb-6">
      <button id="tab-pdf2text" onclick="switchMode('pdf2text')" class="py-2.5 px-4 rounded-lg font-bold text-xs sm:text-sm transition-all duration-150 flex items-center justify-center gap-2 bg-white text-blue-600 shadow-sm">
        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"></path></svg>
        <span>PDF &rarr; Readable Passages</span>
      </button>
      <button id="tab-img2pdf" onclick="switchMode('img2pdf')" class="py-2.5 px-4 rounded-lg font-bold text-xs sm:text-sm transition-all duration-150 flex items-center justify-center gap-2 text-slate-600 hover:text-slate-900">
        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16l4.586-4.586a2 2 0 012.828 0L16 16m-2-2l1.586-1.586a2 2 0 012.828 0L20 14m-6-6h.01M6 20h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z"></path></svg>
        <span>Image &rarr; PDF (30MB+)</span>
      </button>
    </div>

    <!-- Dropzone Area -->
    <div id="dropzone" class="border-2 border-dashed border-blue-400 rounded-xl p-8 text-center cursor-pointer hover:bg-blue-50/50 transition">
      <input type="file" id="fileInput" class="hidden">
      <div class="flex flex-col items-center pointer-events-none">
        <div class="w-12 h-12 bg-blue-50 text-blue-600 rounded-full flex items-center justify-center mb-3">
          <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"></path>
          </svg>
        </div>
        <span id="dropzonePrompt" class="text-slate-700 font-semibold text-sm">Click or Drag & Drop your PDF here</span>
        <span id="dropzoneSub" class="text-xs text-slate-400 mt-1">Accepts large PDFs up to 30 MB+</span>
      </div>
    </div>

    <!-- Mode-Specific Options -->
    <!-- (A) Options for PDF to Readable Passages -->
    <div id="pdfOptions" class="mt-4 flex flex-wrap items-center justify-between text-xs bg-slate-50 p-3 rounded-lg border border-slate-200 gap-2">
      <span class="font-medium text-slate-700">Passage Length:</span>
      <div class="flex items-center gap-3">
        <label class="flex items-center gap-1 cursor-pointer">
          <input type="radio" name="passageLength" value="2" class="text-blue-600"> Short (2 Sentences)
        </label>
        <label class="flex items-center gap-1 cursor-pointer">
          <input type="radio" name="passageLength" value="3" checked class="text-blue-600"> Standard (3-4 Sentences)
        </label>
        <label class="flex items-center gap-1 cursor-pointer">
          <input type="radio" name="passageLength" value="5" class="text-blue-600"> Deep (5+ Sentences)
        </label>
      </div>
    </div>

    <!-- (B) Options for Image to PDF -->
    <div id="imageOptions" class="hidden mt-4 flex items-center justify-between text-xs bg-slate-50 p-3 rounded-lg border border-slate-200">
      <span class="font-medium text-slate-700">Page Sizing:</span>
      <div class="flex items-center gap-4">
        <label class="flex items-center gap-1 cursor-pointer">
          <input type="radio" name="layoutMode" value="a4" checked class="text-blue-600"> Fit to A4
        </label>
        <label class="flex items-center gap-1 cursor-pointer">
          <input type="radio" name="layoutMode" value="original" class="text-blue-600"> Match Image Aspect Ratio
        </label>
      </div>
    </div>

    <!-- Image Previews Container (Image to PDF mode) -->
    <div id="fileQueueContainer" class="mt-4 hidden">
      <div class="flex items-center justify-between text-xs font-semibold text-slate-600 pb-2 border-b border-slate-100">
        <span>Selected Files (<span id="fileCount">0</span>) &bull; <span id="totalSize">0 MB</span></span>
        <button onclick="clearFiles()" class="text-rose-500 hover:underline">Clear all</button>
      </div>
      <div id="thumbnailContainer" class="grid grid-cols-4 sm:grid-cols-5 gap-2 mt-2 max-h-40 overflow-y-auto p-1"></div>
    </div>

    <!-- Progress Tracker -->
    <div id="progressContainer" class="mt-4 hidden">
      <div class="flex justify-between text-xs font-semibold text-slate-600 mb-1">
        <span id="progressStatus">Processing...</span>
        <span id="progressPercent">0%</span>
      </div>
      <div class="w-full bg-slate-200 h-2 rounded-full overflow-hidden">
        <div id="progressBar" class="bg-blue-600 h-full w-0 transition-all duration-150"></div>
      </div>
    </div>

    <!-- Convert / Process Button -->
    <button id="processBtn" onclick="handleProcessing()" class="w-full mt-5 bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 rounded-xl shadow-md transition disabled:opacity-50 disabled:cursor-not-allowed text-sm" disabled>
      Extract & Format into Passages
    </button>

    <!-- Results Section: Formatted Readable Passages -->
    <div id="passageResultContainer" class="hidden mt-6 pt-5 border-t border-slate-200">
      <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 mb-3">
        <div>
          <h2 class="text-sm font-bold text-slate-800 flex items-center gap-2">
            <span class="w-2 h-2 rounded-full bg-emerald-500"></span>
            Readable Passages (<span id="passageCount">0</span> passages &bull; <span id="wordCount">0</span> words)
          </h2>
          <p class="text-[11px] text-slate-500">De-hyphenated, de-chopped, and formatted for comfortable reading.</p>
        </div>
        <div class="flex items-center gap-2">
          <button id="copyPassagesBtn" onclick="copyPassages()" class="px-3 py-1.5 bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold rounded-lg border border-slate-200 transition">
            Copy Text
          </button>
          <button onclick="downloadAsTextFile()" class="px-3 py-1.5 bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold rounded-lg border border-slate-200 transition">
            .TXT
          </button>
          <button onclick="downloadFormattedPassagesPdf()" class="px-3.5 py-1.5 bg-emerald-600 hover:bg-emerald-700 text-white text-xs font-semibold rounded-lg shadow-sm transition">
            Download Clean PDF
          </button>
        </div>
      </div>

      <!-- Scrollable Passages View Container -->
      <div id="passageList" class="space-y-3 max-h-96 overflow-y-auto p-3 bg-slate-50 rounded-xl border border-slate-200">
        <!-- Dynamic Passages Injected Here -->
      </div>
    </div>

  </main>

  <!-- Explanatory SEO Text Card -->
  <article class="w-full max-w-3xl bg-white shadow-sm rounded-xl p-5 mt-6 border border-slate-200 text-xs text-slate-600 leading-relaxed">
    <h3 class="text-sm font-bold text-slate-800 mb-1">Why convert PDFs to short passages?</h3>
    <p>
      Raw PDF text extraction usually results in broken lines, random mid-sentence line returns, and awkward word splits (e.g. <code>inter- \n rupt</code>). Our smart passage restructuring algorithm automatically stitches split lines together, identifies authentic sentence boundaries, and arranges paragraphs into short, digestible passages of 2 to 4 sentences each so readers can absorb content without visual fatigue.
    </p>
  </article>

  <!-- Footer -->
  <footer class="w-full max-w-4xl text-center text-xs text-slate-400 py-6">
    &copy; 2026 OmniDoc Studio. 100% Client-Side. No files ever leave your device.
  </footer>

  <!-- Script Logic -->
  <script>
    // Set PDF.js worker
    pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';
    const { jsPDF } = window.jspdf;

    let currentMode = 'pdf2text'; // 'pdf2text' | 'img2pdf'
    let selectedFiles = [];
    let extractedPassages = [];

    // Elements
    const dropzone = document.getElementById('dropzone');
    const fileInput = document.getElementById('fileInput');
    const dropzonePrompt = document.getElementById('dropzonePrompt');
    const dropzoneSub = document.getElementById('dropzoneSub');
    const pdfOptions = document.getElementById('pdfOptions');
    const imageOptions = document.getElementById('imageOptions');
    const fileQueueContainer = document.getElementById('fileQueueContainer');
    const thumbnailContainer = document.getElementById('thumbnailContainer');
    const fileCount = document.getElementById('fileCount');
    const totalSize = document.getElementById('totalSize');
    const processBtn = document.getElementById('processBtn');
    const progressContainer = document.getElementById('progressContainer');
    const progressBar = document.getElementById('progressBar');
    const progressStatus = document.getElementById('progressStatus');
    const progressPercent = document.getElementById('progressPercent');
    const passageResultContainer = document.getElementById('passageResultContainer');
    const passageList = document.getElementById('passageList');
    const passageCount = document.getElementById('passageCount');
    const wordCount = document.getElementById('wordCount');
    const copyPassagesBtn = document.getElementById('copyPassagesBtn');

    // Switch between modes
    function switchMode(mode) {
      currentMode = mode;
      clearFiles();
      passageResultContainer.classList.add('hidden');

      const tabPdf = document.getElementById('tab-pdf2text');
      const tabImg = document.getElementById('tab-img2pdf');

      if (mode === 'pdf2text') {
        tabPdf.className = "py-2.5 px-4 rounded-lg font-bold text-xs sm:text-sm transition-all duration-150 flex items-center justify-center gap-2 bg-white text-blue-600 shadow-sm";
        tabImg.className = "py-2.5 px-4 rounded-lg font-bold text-xs sm:text-sm transition-all duration-150 flex items-center justify-center gap-2 text-slate-600 hover:text-slate-900";
        
        fileInput.accept = ".pdf";
        fileInput.multiple = false;
        dropzonePrompt.textContent = "Click or Drag & Drop your PDF here";
        dropzoneSub.textContent = "Accepts large PDFs up to 30 MB+";
        pdfOptions.classList.remove('hidden');
        imageOptions.classList.add('hidden');
        processBtn.textContent = "Extract & Format into Passages";
      } else {
        tabImg.className = "py-2.5 px-4 rounded-lg font-bold text-xs sm:text-sm transition-all duration-150 flex items-center justify-center gap-2 bg-white text-blue-600 shadow-sm";
        tabPdf.className = "py-2.5 px-4 rounded-lg font-bold text-xs sm:text-sm transition-all duration-150 flex items-center justify-center gap-2 text-slate-600 hover:text-slate-900";
        
        fileInput.accept = "image/jpeg, image/png, image/webp";
        fileInput.multiple = true;
        dropzonePrompt.textContent = "Click or Drag & Drop Images Here";
        dropzoneSub.textContent = "Supports JPG, PNG, WEBP (Batch files up to 30 MB+)";
        pdfOptions.classList.add('hidden');
        imageOptions.classList.remove('hidden');
        processBtn.textContent = "Generate & Download PDF";
      }
    }

    // Drag and Drop Listeners
    ['dragenter', 'dragover'].forEach(name => {
      dropzone.addEventListener(name, (e) => {
        e.preventDefault();
        dropzone.classList.add('dragover');
      });
    });

    ['dragleave', 'drop'].forEach(name => {
      dropzone.addEventListener(name, (e) => {
        e.preventDefault();
        dropzone.classList.remove('dragover');
      });
    });

    dropzone.addEventListener('click', () => fileInput.click());

    dropzone.addEventListener('drop', (e) => {
      handleFiles(Array.from(e.dataTransfer.files));
    });

    fileInput.addEventListener('change', (e) => {
      handleFiles(Array.from(e.target.files));
      fileInput.value = '';
    });

    function handleFiles(files) {
      if (!files || files.length === 0) return;

      if (currentMode === 'pdf2text') {
        const pdfFile = files.find(f => f.type === 'application/pdf' || f.name.endsWith('.pdf'));
        if (!pdfFile) {
          alert('Please upload a valid PDF document.');
          return;
        }
        selectedFiles = [pdfFile];
      } else {
        const imageFiles = files.filter(f => f.type.startsWith('image/'));
        if (imageFiles.length === 0) {
          alert('Please upload JPG or PNG image files.');
          return;
        }
        selectedFiles = [...selectedFiles, ...imageFiles];
      }

      updateUI();
    }

    function clearFiles() {
      selectedFiles = [];
      updateUI();
    }

    function removeFile(index) {
      selectedFiles.splice(index, 1);
      updateUI();
    }

    function formatBytes(bytes) {
      if (bytes === 0) return '0 MB';
      return (bytes / (1024 * 1024)).toFixed(2) + ' MB';
    }

    function updateUI() {
      if (selectedFiles.length === 0) {
        fileQueueContainer.classList.add('hidden');
        processBtn.disabled = true;
        thumbnailContainer.innerHTML = '';
        return;
      }

      processBtn.disabled = false;

      if (currentMode === 'img2pdf') {
        fileQueueContainer.classList.remove('hidden');
        fileCount.textContent = selectedFiles.length;
        const total = selectedFiles.reduce((sum, f) => sum + f.size, 0);
        totalSize.textContent = formatBytes(total);

        thumbnailContainer.innerHTML = '';
        selectedFiles.forEach((file, idx) => {
          const div = document.createElement('div');
          div.className = 'relative aspect-square rounded-lg overflow-hidden bg-slate-100 border border-slate-200';
          const img = document.createElement('img');
          img.src = URL.createObjectURL(file);
          img.className = 'w-full h-full object-cover';
          img.onload = () => URL.revokeObjectURL(img.src);

          const btn = document.createElement('button');
          btn.className = 'absolute top-1 right-1 bg-black/60 hover:bg-rose-600 text-white p-1 rounded-full text-[10px] leading-none';
          btn.innerHTML = '&times;';
          btn.onclick = (e) => {
            e.stopPropagation();
            removeFile(idx);
          };

          div.appendChild(img);
          div.appendChild(btn);
          thumbnailContainer.appendChild(div);
        });
      } else {
        fileQueueContainer.classList.remove('hidden');
        fileCount.textContent = `1 PDF: ${selectedFiles[0].name}`;
        totalSize.textContent = formatBytes(selectedFiles[0].size);
        thumbnailContainer.innerHTML = `
          <div class="col-span-full p-2 bg-blue-50 border border-blue-200 rounded text-xs text-blue-700 flex items-center justify-between">
            <span class="truncate font-medium">${selectedFiles[0].name} (${formatBytes(selectedFiles[0].size)})</span>
            <span class="text-[10px] bg-blue-200 px-2 py-0.5 rounded text-blue-800 font-semibold">Ready</span>
          </div>
        `;
      }
    }

    // --- Core Action Dispatcher ---
    async function handleProcessing() {
      if (selectedFiles.length === 0) return;

      processBtn.disabled = true;
      progressContainer.classList.remove('hidden');
      passageResultContainer.classList.add('hidden');

      try {
        if (currentMode === 'pdf2text') {
          await processPdfToPassages();
        } else {
          await processImagesToPdf();
        }
      } catch (err) {
        console.error(err);
        alert('An error occurred during processing: ' + err.message);
      } finally {
        progressContainer.classList.add('hidden');
        processBtn.disabled = false;
        progressBar.style.width = '0%';
      }
    }

    // --- 1. PDF to Short Readable Passages Engine ---
    async function processPdfToPassages() {
      const file = selectedFiles[0];
      progressStatus.textContent = 'Opening PDF document...';
      progressPercent.textContent = '5%';
      progressBar.style.width = '5%';

      const arrayBuffer = await file.arrayBuffer();
      const loadingTask = pdfjsLib.getDocument({ data: arrayBuffer });
      const pdf = await loadingTask.promise;
      const numPages = pdf.numPages;

      let aggregatedRawText = '';

      for (let p = 1; p <= numPages; p++) {
        const pct = Math.round((p / numPages) * 75) + 5;
        progressPercent.textContent = `${pct}%`;
        progressBar.style.width = `${pct}%`;
        progressStatus.textContent = `Extracting page ${p} of ${numPages}...`;

        // Yield to allow UI refresh on 30MB+ PDFs
        if (p % 2 === 0) await new Promise(r => setTimeout(r, 0));

        const page = await pdf.getPage(p);
        const textContent = await page.getTextContent();
        
        // Assemble text while respecting soft line-breaks
        const pageLines = [];
        let currentLine = '';

        for (const item of textContent.items) {
          currentLine += item.str + ' ';
          if (item.hasEOL) {
            pageLines.push(currentLine.trim());
            currentLine = '';
          }
        }
        if (currentLine.trim()) pageLines.push(currentLine.trim());

        aggregatedRawText += pageLines.join('\n') + '\n\n';
      }

      progressStatus.textContent = 'Formatting into easily readable passages...';
      progressBar.style.width = '90%';
      await new Promise(r => setTimeout(r, 20));

      const passageLengthSetting = parseInt(document.querySelector('input[name="passageLength"]:checked').value);
      extractedPassages = buildReadablePassages(aggregatedRawText, passageLengthSetting);

      renderPassages(extractedPassages);
    }

    // Restructuring algorithm: converts fragmented lines into fluent short passages
    function buildReadablePassages(rawText, sentencesPerPassage = 3) {
      if (!rawText.trim()) return [];

      // 1. Remove hyphenated word-breaks across lines: "inter-\nrupt" -> "interrupt"
      let text = rawText.replace(/(\b\w+)-\s*\n\s*(\w+\b)/g, '$1$2');

      // 2. Normalize whitespace
      text = text.replace(/[ \t]+/g, ' ');

      // 3. Connect lines that don't end in standard sentence-stopping punctuation
      const lines = text.split('\n').map(l => l.trim()).filter(Boolean);
      let unifiedText = '';

      for (let i = 0; i < lines.length; i++) {
        const line = lines[i];
        if (!unifiedText) {
          unifiedText = line;
          continue;
        }

        const prevEndsWithPunct = /[.!?:]$/.test(unifiedText.trim());
        const isBulletOrHeader = /^([•\-\*]|\d+[\.\)]|[A-Z\s]{4,}:)/.test(line);

        if (!prevEndsWithPunct && !isBulletOrHeader) {
          unifiedText += ' ' + line;
        } else {
          unifiedText += '\n\n' + line;
        }
      }

      // 4. Split into sentence units and package into 2-3 sentence passages
      const rawBlocks = unifiedText.split(/\n\s*\n+/).filter(Boolean);
      const finalPassages = [];

      for (const block of rawBlocks) {
        // Keep standalone headers or short bullets intact
        if (block.length < 75 && !/[.!?]$/.test(block)) {
          finalPassages.push(block);
          continue;
        }

        // Sentence splitter
        const sentences = block.match(/[^.!?]+[.!?]+["']?|[^.!?]+$/g) || [block];
        let currentBatch = [];

        for (const sentence of sentences) {
          const sTrimmed = sentence.trim();
          if (!sTrimmed) continue;
          currentBatch.push(sTrimmed);

          if (currentBatch.length >= sentencesPerPassage) {
            finalPassages.push(currentBatch.join(' '));
            currentBatch = [];
          }
        }

        if (currentBatch.length > 0) {
          finalPassages.push(currentBatch.join(' '));
        }
      }

      return finalPassages;
    }

    function renderPassages(passages) {
      passageList.innerHTML = '';
      if (passages.length === 0) {
        passageList.innerHTML = '<p class="text-xs text-slate-400 italic">No readable text found in document.</p>';
        return;
      }

      passageCount.textContent = passages.length;
      const totalWords = passages.reduce((acc, p) => acc + p.split(/\s+/).length, 0);
      wordCount.textContent = totalWords;

      passages.forEach((p, idx) => {
        const div = document.createElement('div');
        div.className = 'bg-white p-3.5 rounded-lg border border-slate-200/80 shadow-sm text-xs text-slate-700 leading-relaxed';
        div.innerHTML = `
          <div class="flex items-center justify-between text-[10px] font-mono text-slate-400 mb-1">
            <span>Passage ${idx + 1}</span>
            <span>${p.split(/\s+/).length} words</span>
          </div>
          <p>${p}</p>
        `;
        passageList.appendChild(div);
      });

      passageResultContainer.classList.remove('hidden');
    }

    function copyPassages() {
      const fullText = extractedPassages.join('\n\n');
      navigator.clipboard.writeText(fullText).then(() => {
        const orig = copyPassagesBtn.textContent;
        copyPassagesBtn.textContent = 'Copied!';
        setTimeout(() => copyPassagesBtn.textContent = orig, 1800);
      });
    }

    function downloadAsTextFile() {
      const fullText = extractedPassages.join('\n\n');
      const blob = new Blob([fullText], { type: 'text/plain;charset=utf-8' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = `${selectedFiles[0].name.replace(/\.[^/.]+$/, "")}_readable_passages.txt`;
      a.click();
      URL.revokeObjectURL(url);
    }

    // Export cleanly spaced passages into a new PDF
    function downloadFormattedPassagesPdf() {
      const pdf = new jsPDF('p', 'mm', 'a4');
      const margin = 18;
      const pageWidth = 210 - (margin * 2);
      const pageHeight = 297;
      let cursorY = 22;

      pdf.setFont('helvetica', 'normal');
      pdf.setFontSize(10);
      pdf.setTextColor(45, 55, 72);

      extractedPassages.forEach((passage, idx) => {
        const lines = pdf.splitTextToSize(passage, pageWidth);
        const blockHeight = lines.length * 4.8;

        if (cursorY + blockHeight > pageHeight - margin) {
          pdf.addPage();
          cursorY = margin;
        }

        pdf.text(lines, margin, cursorY);
        cursorY += blockHeight + 6; // Clean spacing between readable passages
      });

      pdf.save('readable_passages.pdf');
    }

    // --- 2. High-Performance Image to PDF Engine (30MB+ Safe) ---
    async function compressImage(file, maxDim = 2000, quality = 0.85) {
      const bitmap = await createImageBitmap(file);
      let { width, height } = bitmap;

      if (width > maxDim || height > maxDim) {
        if (width > height) {
          height = Math.round((height * maxDim) / width);
          width = maxDim;
        } else {
          width = Math.round((width * maxDim) / height);
          height = maxDim;
        }
      }

      const canvas = document.createElement('canvas');
      canvas.width = width;
      canvas.height = height;
      const ctx = canvas.getContext('2d');
      ctx.drawImage(bitmap, 0, 0, width, height);

      const dataUrl = canvas.toDataURL('image/jpeg', quality);

      bitmap.close();
      canvas.width = 0;
      canvas.height = 0;

      return { dataUrl, width, height };
    }

    async function processImagesToPdf() {
      const layoutMode = document.querySelector('input[name="layoutMode"]:checked').value;
      const pdf = new jsPDF('p', 'mm', 'a4');
      const total = selectedFiles.length;
      let isFirstPage = true;

      for (let i = 0; i < total; i++) {
        const pct = Math.round(((i + 1) / total) * 100);
        progressPercent.textContent = `${pct}%`;
        progressBar.style.width = `${pct}%`;
        progressStatus.textContent = `Optimizing image ${i + 1} of ${total}...`;

        await new Promise(r => setTimeout(r, 10));

        const { dataUrl, width, height } = await compressImage(selectedFiles[i], 2200, 0.84);

        if (layoutMode === 'a4') {
          if (!isFirstPage) pdf.addPage('a4', 'p');
          isFirstPage = false;

          const pWidth = pdf.internal.pageSize.getWidth();
          const pHeight = pdf.internal.pageSize.getHeight();

          let rWidth = pWidth - 20;
          let rHeight = (height * rWidth) / width;

          if (rHeight > pHeight - 20) {
            rHeight = pHeight - 20;
            rWidth = (width * rHeight) / height;
          }

          const posX = (pWidth - rWidth) / 2;
          const posY = (pHeight - rHeight) / 2;
          pdf.addImage(dataUrl, 'JPEG', posX, posY, rWidth, rHeight, undefined, 'FAST');
        } else {
          const orientation = width > height ? 'l' : 'p';
          if (!isFirstPage) {
            pdf.addPage([width, height], orientation);
          } else {
            pdf.deletePage(1);
            pdf.addPage([width, height], orientation);
            isFirstPage = false;
          }
          pdf.addImage(dataUrl, 'JPEG', 0, 0, width, height, undefined, 'FAST');
        }
      }

      progressStatus.textContent = 'Saving PDF file...';
      await new Promise(r => setTimeout(r, 20));
      pdf.save('converted_images.pdf');
    }

    // Default initialization
    switchMode('pdf2text');
  </script>
</body>
</html>
