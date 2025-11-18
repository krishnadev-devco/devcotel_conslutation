<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DevCotel Free Career Consultation</title>
    <!-- Load Supabase Client -->
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        'background-dark': '#1e1e1e',
                        'form-dark': '#2e2e2e',
                        'text-light': '#f0f0f0',
                        'accent-blue': '#4c7cff',
                        'accent-purple': '#a855f7',
                        'active-border': '#34d399', /* Green checkmark color from image */
                        'button-bg': '#6d28d9', /* A deep purple for the main button */
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    },
                },
            }
        }
    </script>
    <style>
        /* Base Dark Style & Responsive View */
        body {
            background-color: #000;
            color: var(--text-light);
            font-family: 'Inter', sans-serif;
            display: flex;
            flex-direction: column;
            /* Changed to min-height and allowed scroll for responsiveness */
            min-height: 100vh; 
            margin: 0;
            overflow-x: hidden;
            overflow-y: auto; 
        }
        
        #app-container {
            background-color: var(--background-dark);
            width: 100%;
            max-width: 450px; /* Constrain width for a 'card' style on desktop */
            min-height: 100vh; /* Ensure the card extends full height on mobile */
            display: flex;
            flex-direction: column;
            margin-left: auto;
            margin-right: auto;
            position: relative;
        }

        /* 1. Progress Bar Styles (Segments) */
        .progress-bar-segment {
            height: 4px;
            background-color: #333;
            transition: background-color 0.4s ease;
        }
        .progress-bar-segment.completed {
            background: linear-gradient(to right, #ff7e5f, #feb47b); 
        }
        .progress-bar-segment.active {
            background: linear-gradient(to right, #a855f7, #6d28d9);
        }

        /* 2. Custom Radio/Checkbox Styling */
        .custom-radio {
            display: inline-block;
            width: 24px;
            height: 24px;
            border-radius: 50%;
            border: 2px solid #666; 
            position: relative;
            cursor: pointer;
            transition: all 0.2s ease;
        }
        .custom-radio::after {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%) scale(0);
            width: 10px;
            height: 10px;
            border-radius: 50%;
            background-color: white; 
            transition: transform 0.2s ease;
        }
        input[type="radio"]:checked + .custom-radio {
            border-color: white;
            background-color: transparent;
        }
        input[type="radio"]:checked + .custom-radio::after {
            transform: translate(-50%, -50%) scale(1);
        }

        /* 3. Input Field Style */
        .form-input-box {
            background-color: #333;
            border: none;
            color: var(--text-light);
            padding: 1rem;
            border-radius: 8px;
            resize: none; 
            box-shadow: 0 0 0 2px transparent;
            transition: box-shadow 0.2s ease;
        }
        .form-input-box:focus {
            outline: none;
            box-shadow: 0 0 0 2px var(--accent-blue);
        }
        .form-input-box.ring-red-500 {
            box-shadow: 0 0 0 2px #ef4444;
        }
        
        /* 4. Continue Button */
        #continue-btn {
            background-color: var(--button-bg);
            color: white;
            transition: background-color 0.2s ease;
            font-weight: 600;
        }
        #continue-btn:hover:not(:disabled) {
            background-color: #5b21b6;
        }
        #continue-btn:disabled {
            background-color: #4a4a4a;
            cursor: not-allowed;
        }

        /* 5. Step Content for flow-based navigation (removing absolute positioning and inner scroll) */
        .step-content {
            padding: 24px 20px;
            /* Opacity for fade transition. Hide/Show is handled by the 'hidden' class */
            opacity: 0;
            transition: opacity 0.3s ease-in;
        }
        .step-content.active {
            opacity: 1;
        }
    </style>
</head>
<body>

<div id="app-container">

    <!-- Header: Icons and Progress Bar -->
    <header class="p-4 pt-8 pb-4">
        <!-- Icons -->
        <div class="flex justify-between items-center mb-4">
            <button onclick="prevStep()" aria-label="Go Back">
                <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18"></path></svg>
            </button>
            <button onclick="showMessage('Form Closed')" aria-label="Close Form">
                <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
            </button>
        </div>

        <!-- Progress Bar -->
        <div id="progress-bar" class="flex space-x-1.5">
            <div class="progress-bar-segment w-1/4 rounded-full" id="prog-1"></div>
            <div class="progress-bar-segment w-1/4 rounded-full" id="prog-2"></div>
            <div class="progress-bar-segment w-1/4 rounded-full" id="prog-3"></div>
            <div class="progress-bar-segment w-1/4 rounded-full" id="prog-4"></div>
        </div>
    </header>

    <!-- Main Content Area (Flexible Height) -->
    <div class="flex-1">
        <div class="text-center mb-6 pt-4">
            <!-- Profile Image Placeholder -->
            <div class="w-16 h-16 rounded-full bg-gray-600 mx-auto mb-2 flex items-center justify-center overflow-hidden">
                <img src="https://repository-images.githubusercontent.com/699901326/52451cd7-9fda-4e59-8ce5-6bd93ff287d6" alt="Profile" class="w-full h-full object-cover">
            </div>
            <p class="font-semibold text-lg text-white">devcotel</p>
            <!-- Class is controlled by JS to toggle between text-gray-400 and text-white -->
            <p id="sub-header-text" class="text-sm text-gray-400 mt-1 px-4"> Every one have rights to get good quality of education at a good price. </p>
        </div>

        <!-- Form Steps Container (Now uses natural flow) -->
        <div id="form-steps-container" class="relative flex-1">
            
            <!-- Step 1: Experience Level (Radio) -->
            <div id="step-1" class="step-content active pt-0">
                <h2 class="text-xl font-bold mb-6 pt-4 text-white">How many years of work experience do you have?</h2>
                <div class="space-y-4">
                    <!-- Option 1 -->
                    <label class="flex items-center justify-between p-4 bg-form-dark rounded-xl cursor-pointer hover:bg-gray-700 transition duration-150">
                        <span class="text-white text-base font-medium">I'm a student</span>
                        <input type="radio" name="experience" value="student" class="hidden" data-required="true">
                        <span class="custom-radio"></span>
                    </label>
                    <!-- Option 2 -->
                    <label class="flex items-center justify-between p-4 bg-form-dark rounded-xl cursor-pointer hover:bg-gray-700 transition duration-150">
                        <span class="text-white text-base font-medium">Recent Graduate</span>
                        <input type="radio" name="experience" value="recent_grad" class="hidden" data-required="true">
                        <span class="custom-radio"></span>
                    </label>
                    <!-- Option 3 (Selected in image) -->
                    <label class="flex items-center justify-between p-4 bg-form-dark rounded-xl cursor-pointer hover:bg-gray-700 transition duration-150">
                        <span class="text-white text-base font-medium">0.5-2 years</span>
                        <input type="radio" name="experience" value="0.5-2" class="hidden" checked data-required="true">
                        <span class="custom-radio"></span>
                    </label>
                    <!-- Option 4 -->
                    <label class="flex items-center justify-between p-4 bg-form-dark rounded-xl cursor-pointer hover:bg-gray-700 transition duration-150">
                        <span class="text-white text-base font-medium">2-5 years</span>
                        <input type="radio" name="experience" value="2-5" class="hidden" data-required="true">
                        <span class="custom-radio"></span>
                    </label>
                    <!-- Option 5 -->
                    <label class="flex items-center justify-between p-4 bg-form-dark rounded-xl cursor-pointer hover:bg-gray-700 transition duration-150">
                        <span class="text-white text-base font-medium">5+ years</span>
                        <input type="radio" name="experience" value="5+" class="hidden" data-required="true">
                        <span class="custom-radio"></span>
                    </label>
                </div>
            </div>

            <!-- Step 2: Programming Languages (Text Input) -->
            <div id="step-2" class="step-content hidden pt-0">
                <h2 class="text-xl font-bold mb-6 pt-4 text-white">Which course you are lloking for to join? (e.g. data science, machine learning, cyber security, python) or describe what you want to become <..></h2>
                <div class="relative">
                    <textarea id="languages" name="languages" rows="4" placeholder="Your answer" required class="form-input-box w-full"></textarea>
                    <p id="input-status-2" class="text-active-border text-sm mt-2 hidden flex items-center">
                        <svg class="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path></svg>
                        Input recorded
                    </p>
                </div>
            </div>

            <!-- Step 3: Contact Details (Text Inputs, including Address & Pincode) -->
            <div id="step-3" class="step-content hidden pt-0">
                <h2 class="text-xl font-bold mb-6 pt-4 text-white">Contact & Location Details</h2>
                
                <label for="mobile" class="block text-white text-sm font-medium mb-2">Mobile Number*</label>
                <input type="tel" id="mobile" name="mobile" placeholder="Your answer" required class="form-input-box w-full mb-6">

                <label for="address" class="block text-white text-sm font-medium mb-2">Full Address*</label>
                <textarea id="address" name="address" rows="3" placeholder="Street Address, City, State" required class="form-input-box w-full mb-6"></textarea>

                <label for="pincode" class="block text-white text-sm font-medium mb-2">Pincode / Postal Code*</label>
                <input type="text" id="pincode" name="pincode" placeholder="Your answer (e.g., 123456)" required class="form-input-box w-full mb-6">

                <p id="input-status-3" class="text-active-border text-sm mt-2 hidden flex items-center">
                    <svg class="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path></svg>
                    Input recorded
                </p>
            </div>

            <!-- Step 4: Paid Program Interest (Radio) -->
            <div id="step-4" class="step-content hidden pt-0">
                <h2 class="text-xl font-bold mb-6 pt-4 text-white">Are you open to joining a paid program to accelerate your growth?</h2>
                <div class="space-y-4">
                    <!-- Option 1 -->
                    <label class="flex items-center justify-between p-4 bg-form-dark rounded-xl cursor-pointer hover:bg-gray-700 transition duration-150">
                        <span class="text-white text-base font-medium">Yes, I will join</span>
                        <input type="radio" name="program_interest" value="join" class="hidden" data-required="true">
                        <span class="custom-radio"></span>
                    </label>
                    <!-- Option 2 -->
                    <label class="flex items-center justify-between p-4 bg-form-dark rounded-xl cursor-pointer hover:bg-gray-700 transition duration-150">
                        <span class="text-white text-base font-medium">Yes, but I need some Guidance</span>
                        <input type="radio" name="program_interest" value="guidance" class="hidden" data-required="true">
                        <span class="custom-radio"></span>
                    </label>
                    <!-- Option 3 -->
                    <label class="flex items-center justify-between p-4 bg-form-dark rounded-xl cursor-pointer hover:bg-gray-700 transition duration-150">
                        <span class="text-white text-base font-medium">No, I will not join</span>
                        <input type="radio" name="program_interest" value="no" class="hidden" data-required="true">
                        <span class="custom-radio"></span>
                    </label>
                </div>
            </div>

        </div>
    </div>
    
    <!-- Footer: Continue Button (This section sticks to the bottom of the content) -->
    <footer class="p-4 pt-0 mt-auto">
        <button id="continue-btn" onclick="nextStep()" class="w-full py-3 rounded-xl transition duration-150">
            Continue
        </button>
    </footer>

</div>

<!-- Custom Message Modal (Instead of alert()) -->
<div id="message-modal" class="hidden fixed inset-0 bg-black bg-opacity-75 flex items-center justify-center p-4 z-50">
    <div class="bg-white p-6 rounded-lg shadow-xl max-w-sm w-full">
        <h3 class="text-xl font-bold text-gray-800 mb-4">Notification</h3>
        <p id="modal-text" class="text-gray-600 mb-6">Message content.</p>
        <button onclick="closeMessage()" class="w-full py-2 bg-accent-blue text-white font-semibold rounded-lg hover:bg-blue-600 transition">Close</button>
    </div>
</div>

<script>
    const { createClient } = supabase;
    const supabaseUrl = 'https://xvpmmyqihecilgneiljv.supabase.co';
    const supabaseKey = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Inh2cG1teXFpaGVjaWxnbmVpbGp2Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3NjI0MTE4OTIsImV4cCI6MjA3Nzk4Nzg5Mn0.V4WsMnFXm6XHPJWq80WvO2eaL3hknFFmiVu_t8UMv4E';
    const supabaseClient = createClient(supabaseUrl, supabaseKey);

    let currentStep = 1;
    const totalSteps = 4;
    const stepsData = {}; // To store the form data

    const continueBtn = document.getElementById('continue-btn');
    const progressBar = document.getElementById('progress-bar');
    const stepContents = document.querySelectorAll('.step-content');
    const subHeaderText = document.getElementById('sub-header-text');
    const headers = [
        "Free Career Consultation",
        "Your Background",
        "Contact Information",
        "Program Interest"
    ];

    // --- Utility Functions (Modal) ---
    function showMessage(text) {
        document.getElementById('modal-text').textContent = text;
        document.getElementById('message-modal').classList.remove('hidden');
        document.getElementById('app-container').classList.add('blur-sm');
    }

    function closeMessage() {
        document.getElementById('message-modal').classList.add('hidden');
        document.getElementById('app-container').classList.remove('blur-sm');
    }

    // --- Validation and State Management ---

    function validateStep(step) {
        const stepElement = document.getElementById(`step-${step}`);
        let isValid = true;
        
        // 1. Validate Radio/Checkbox groups
        const radioGroups = stepElement.querySelectorAll('input[type="radio"]');
        if (radioGroups.length > 0) {
            const groupName = radioGroups[0].name;
            const checkedRadio = stepElement.querySelector(`input[name="${groupName}"]:checked`);
            const isChecked = !!checkedRadio;

            if (!isChecked) {
                isValid = false;
            }
            if (isValid) {
                stepsData[groupName] = checkedRadio.value;
            }
        }

        // 2. Validate Text/Textarea inputs
        const inputs = stepElement.querySelectorAll('input[required], textarea[required]');
        inputs.forEach(input => {
            if (!input.value.trim()) {
                isValid = false;
                // Use Tailwind's ring classes for visibility
                input.classList.add('ring-2', 'ring-red-500'); 
            } else {
                input.classList.remove('ring-2', 'ring-red-500');
                stepsData[input.id] = input.value.trim();
            }
        });

        return isValid;
    }

    function updateProgressBar() {
        const segments = progressBar.children;
        for (let i = 0; i < totalSteps; i++) {
            segments[i].classList.remove('completed', 'active');
            if (i < currentStep - 1) {
                segments[i].classList.add('completed');
            } else if (i === currentStep - 1) {
                segments[i].classList.add('active');
            }
        }
    }

    function updateStepView() {
        // 1. Update Step Visibility (Using display: none for responsiveness)
        stepContents.forEach(step => {
            step.classList.remove('active');
            step.classList.add('hidden');
        });

        const currentStepElement = document.getElementById(`step-${currentStep}`);
        if (currentStepElement) {
            // Use a short delay to allow the 'hidden' removal and fade transition
            setTimeout(() => {
                currentStepElement.classList.remove('hidden');
                currentStepElement.classList.add('active');
            }, 50);
        }
        
        // 2. Update header and progress
        subHeaderText.textContent = headers[currentStep - 1];
        
        // Custom color logic: Make "Your Background" (Step 2) white, others gray-400
        if (currentStep === 2) {
            subHeaderText.classList.add('text-white');
            subHeaderText.classList.remove('text-gray-400');
        } else {
            subHeaderText.classList.add('text-gray-400');
            subHeaderText.classList.remove('text-white');
        }

        updateProgressBar();

        // 3. Update button text for final step
        if (currentStep === totalSteps) {
            continueBtn.textContent = 'Submit Consultation Request';
        } else {
            continueBtn.textContent = 'Continue';
        }

        // Scroll the main document to the top of the app container when switching steps
        // This ensures the user sees the new step's content fully.
        document.getElementById('app-container').scrollIntoView({ behavior: 'smooth' });
    }

    function nextStep() {
        // 1. Validate the current step
        if (!validateStep(currentStep)) {
            showMessage("Please complete the required fields to continue.");
            return;
        }
        
        // 1.5. Display success indicator for text steps (2 and 3)
        if (currentStep === 2 || currentStep === 3) {
            const statusElement = document.getElementById(`input-status-${currentStep}`);
            if (statusElement) {
                statusElement.classList.remove('hidden');
            }
        }

        // 2. Advance step or submit
        if (currentStep < totalSteps) {
            currentStep++;
            updateStepView();
        } else {
            // Final submission logic
            handleSubmit();
        }
    }

    function prevStep() {
        if (currentStep > 1) {
            // Hide the status indicator of the step we are leaving
            if (currentStep === 2 || currentStep === 3) {
                const statusElement = document.getElementById(`input-status-${currentStep}`);
                if (statusElement) {
                    statusElement.classList.add('hidden');
                }
            }
            currentStep--;
            updateStepView();
        } else {
            showMessage("You are on the first step.");
        }
    }

    async function handleSubmit() {
        continueBtn.disabled = true;
        continueBtn.textContent = 'Submitting...';
        
        try {
            // Assume the table is called 'consultation_requests' and columns match the keys in stepsData.
            const { data, error } = await supabaseClient
                .from('consultation_requests')
                .insert([stepsData]);

            if (error) {
                throw error;
            }

            console.log("--- FORM SUBMITTED TO SUPABASE ---");
            console.log(stepsData);

            continueBtn.textContent = 'Request Sent!';
            continueBtn.style.backgroundColor = 'var(--active-border)'; // Green for success
            
            showMessage("Thank you! Your consultation request has been successfully submitted. We will contact you shortly.");

        } catch (error) {
            console.error("Supabase submission error:", error.message);
            showMessage("There was an error submitting your request. Please try again.");
            continueBtn.disabled = false;
            continueBtn.textContent = 'Submit Consultation Request';
        }
    }

    // Initialize the view and add input listeners
    document.addEventListener('DOMContentLoaded', () => {
        updateStepView();

        // Add event listeners to remove error classes on input
        stepContents.forEach(stepElement => {
            const inputs = stepElement.querySelectorAll('input, textarea');
            inputs.forEach(input => {
                input.addEventListener('input', () => {
                    // Remove error ring when user starts typing
                    input.classList.remove('ring-2', 'ring-red-500');
                });
            });
        });
        
    });

</script>
</body>
</html>
