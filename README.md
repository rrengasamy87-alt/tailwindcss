<!DOCTYPE html>
<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Medical Prescription Verification</title>
    <!-- Use Tailwind CSS for rapid, responsive styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom styles for a cleaner UI */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0c0a09; /* Stone-950 */
            color: #d1d5db; /* Gray-400 */
        }
        .container {
            max-width: 900px;
        }
    </style>
</head>
<body class="bg-zinc-950 text-zinc-100 min-h-screen flex items-center justify-center p-4">
    <div class="container mx-auto p-8 rounded-2xl shadow-2xl bg-zinc-900 border border-zinc-700">
        <header class="text-center mb-12">
            <h1 class="text-4xl sm:text-5xl md:text-6xl font-extrabold text-white mb-2">
                <span class="bg-clip-text text-transparent bg-gradient-to-r from-teal-400 to-sky-500">
                    AI Medical Prescription Verification
                </span>
            </h1>
            <p class="text-lg text-zinc-400">Leveraging AI for accurate and secure prescription processing.</p>
        </header>

        <main class="grid grid-cols-1 md:grid-cols-2 gap-10">
            <!-- Upload and Action Section -->
            <section class="bg-zinc-800 p-8 rounded-xl shadow-inner border border-zinc-700">
                <h2 class="text-2xl font-bold text-white mb-4">Upload & Verify</h2>
                <div class="flex flex-col items-center justify-center p-6 border-2 border-dashed border-zinc-600 rounded-lg mb-6 cursor-pointer hover:border-sky-500 transition-colors duration-200">
                    <input type="file" id="prescription-upload" class="hidden" accept="image/*">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-16 h-16 text-zinc-500 mb-2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M3 16.5v2.25A2.25 2.25 0 0 0 5.25 21h13.5A2.25 2.25 0 0 0 21 18.75V16.5m-13.5-9L12 3m0 0 4.5 4.5M12 3v13.5" />
                    </svg>
                    <p class="text-sm text-zinc-400">Drag & drop your prescription image, or <span class="text-sky-400 font-medium">click to upload</span></p>
                    <p class="text-xs text-zinc-500 mt-1">(PNG, JPG, PDF up to 5MB)</p>
                </div>
                <div id="uploaded-image-container" class="mb-6 hidden">
                    <h3 class="text-lg font-semibold text-white mb-2">Uploaded Image Preview</h3>
                    <img id="uploaded-image" src="#" alt="Uploaded Prescription" class="w-full h-auto rounded-lg shadow-lg border border-zinc-600">
                </div>
                <button id="verify-button" class="w-full py-3 px-6 rounded-full font-bold text-lg text-white transition-all duration-200 bg-gradient-to-r from-sky-500 to-teal-500 hover:from-sky-600 hover:to-teal-600 focus:outline-none focus:ring-4 focus:ring-sky-500 focus:ring-opacity-50">
                    Verify Prescription
                </button>
            </section>

            <!-- Results Section -->
            <section class="bg-zinc-800 p-8 rounded-xl shadow-inner border border-zinc-700">
                <h2 class="text-2xl font-bold text-white mb-4">Verification Results</h2>
                <div id="status-message" class="text-center p-4 rounded-lg bg-zinc-700 text-zinc-300 font-semibold mb-6">
                    Awaiting Upload...
                </div>
                <div id="results-display" class="space-y-4 hidden">
                    <!-- Results will be injected here by JavaScript -->
                </div>
            </section>
        </main>

        <!-- Technology Explanation Section -->
        <section class="mt-16 text-center">
            <h2 class="text-3xl font-bold text-white mb-6">How It Works (Conceptual)</h2>
            <div class="flex flex-col md:flex-row justify-center items-stretch gap-8">
                <!-- IBM Watson Card -->
                <div class="bg-zinc-800 p-6 rounded-xl shadow-md border border-zinc-700 flex-1">
                    <img src="https://logowik.com/hide-download/5300589" alt="IBM Watson Logo" class="w-24 h-auto mx-auto mb-4 grayscale hover:grayscale-0 transition-all duration-300">
                    <h3 class="text-xl font-semibold text-white mb-2">IBM Watson</h3>
                    <p class="text-sm text-zinc-400">
                        In a real application, IBM Watson's Natural Language Processing (NLP) capabilities would be used to understand and extract key medical entities from the text. This includes drug names, dosages, frequencies, and patient information, ensuring all data is correctly parsed for verification against a medical database.
                    </p>
                </div>
                <!-- Hugging Face Card -->
                <div class="bg-zinc-800 p-6 rounded-xl shadow-md border border-zinc-700 flex-1">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/b/b2/Hugging_Face_logo.svg" alt="Hugging Face Logo" class="w-24 h-auto mx-auto mb-4 grayscale hover:grayscale-0 transition-all duration-300">
                    <h3 class="text-xl font-semibold text-white mb-2">Hugging Face Models</h3>
                    <p class="text-sm text-zinc-400">
                        Hugging Face models, particularly those for Optical Character Recognition (OCR), would be crucial for the first step: converting the image of the handwritten or printed prescription into machine-readable text. This process, often powered by Transformer models, ensures high accuracy even with complex or faint text.
                    </p>
                </div>
            </div>
        </section>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const uploadInput = document.getElementById('prescription-upload');
            const uploadContainer = document.querySelector('.flex.flex-col.items-center');
            const verifyButton = document.getElementById('verify-button');
            const statusMessage = document.getElementById('status-message');
            const resultsDisplay = document.getElementById('results-display');
            const uploadedImage = document.getElementById('uploaded-image');
            const uploadedImageContainer = document.getElementById('uploaded-image-container');

            let uploadedFile = null;

            // Handle file selection from click
            uploadContainer.addEventListener('click', () => {
                uploadInput.click();
            });

            // Handle file drop for drag-and-drop functionality
            uploadContainer.addEventListener('dragover', (e) => {
                e.preventDefault();
                uploadContainer.classList.add('border-sky-500', 'bg-zinc-800');
            });

            uploadContainer.addEventListener('dragleave', () => {
                uploadContainer.classList.remove('border-sky-500', 'bg-zinc-800');
            });

            uploadContainer.addEventListener('drop', (e) => {
                e.preventDefault();
                uploadContainer.classList.remove('border-sky-500', 'bg-zinc-800');
                handleFile(e.dataTransfer.files[0]);
            });

            uploadInput.addEventListener('change', (e) => {
                handleFile(e.target.files[0]);
            });

            function handleFile(file) {
                if (!file || !file.type.startsWith('image/')) {
                    statusMessage.textContent = 'Please upload a valid image file (PNG, JPG).';
                    return;
                }

                uploadedFile = file;
                statusMessage.textContent = File selected: ${file.name};
                uploadedImageContainer.classList.remove('hidden');

                // Read file and display a preview
                const reader = new FileReader();
                reader.onload = (e) => {
                    uploadedImage.src = e.target.result;
                };
                reader.readAsDataURL(file);
            }

            verifyButton.addEventListener('click', () => {
                if (!uploadedFile) {
                    statusMessage.textContent = 'Please upload a prescription image first.';
                    return;
                }

                // Simulate the AI verification process
                statusMessage.textContent = 'Verifying with AI models...';
                statusMessage.classList.add('animate-pulse');
                resultsDisplay.classList.add('hidden');
                
                // Simulate a network delay
                setTimeout(() => {
                    statusMessage.classList.remove('animate-pulse');
                    const mockResult = {
                        status: 'Verified',
                        prescriptionDetails: {
                            patientName: 'John Doe',
                            drugName: 'Amoxicillin',
                            dosage: '500mg',
                            frequency: 'Twice a day',
                            doctorName: 'Dr. Jane Smith',
                            comments: 'Prescription details successfully extracted and validated.',
                        },
                        potentialIssues: 'No issues found.',
                    };

                    displayResults(mockResult);
                }, 3000); // 3-second delay
            });

            function displayResults(result) {
                if (result.status === 'Verified') {
                    statusMessage.textContent = 'Verification Complete: Prescription Verified!';
                    statusMessage.classList.remove('bg-zinc-700');
                    statusMessage.classList.add('bg-teal-500', 'text-white');

                    resultsDisplay.innerHTML = `
                        <div class="space-y-4">
                            <div class="flex items-center space-x-2">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-teal-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                                </svg>
                                <p class="text-lg font-bold text-white">Verification Status: <span class="text-teal-400">${result.status}</span></p>
                            </div>
                            <ul class="space-y-2 text-zinc-400">
                                <li><strong>Patient Name:</strong> ${result.prescriptionDetails.patientName}</li>
                                <li><strong>Drug Name:</strong> ${result.prescriptionDetails.drugName}</li>
                                <li><strong>Dosage:</strong> ${result.prescriptionDetails.dosage}</li>
                                <li><strong>Frequency:</strong> ${result.prescriptionDetails.frequency}</li>
                                <li><strong>Doctor:</strong> ${result.prescriptionDetails.doctorName}</li>
                            </ul>
                            <div class="p-4 rounded-lg bg-zinc-700 border border-zinc-600">
                                <p class="font-bold text-zinc-300">AI Analysis:</p>
                                <p class="text-sm text-zinc-400">${result.prescriptionDetails.comments}</p>
                            </div>
                        </div>
                    `;
                } else {
                     statusMessage.textContent = 'Verification failed. Please try again.';
                     statusMessage.classList.remove('bg-zinc-700');
                     statusMessage.classList.add('bg-red-500', 'text-white');
                     resultsDisplay.innerHTML = `
                        <p class="text-center text-red-400 font-semibold">Could not verify the prescription. Possible reasons: unclear image, or missing key information.</p>
                     `;
                }
                
                resultsDisplay.classList.remove('hidden');
            }
        });
    </script>
</body>
</html>
