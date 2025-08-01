<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSI Attribute Portal</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Inter Font -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6; /* Light gray background */
        }
        /* Custom scrollbar for better aesthetics */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #e0e0e0;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb {
            background: #9ca3af; /* Gray-400 */
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #6b7280; /* Gray-500 */
        }
        /* Visual indentation for nested attributes */
        .attribute-input-row[data-level="0"] { margin-left: 0rem; }
        .attribute-input-row[data-level="1"] { margin-left: 1.5rem; }
        .attribute-input-row[data-level="2"] { margin-left: 3rem; }
        .attribute-input-row[data-level="3"] { margin-left: 4.5rem; }
        .attribute-input-row[data-level="4"] { margin-left: 6rem; }
        /* Add more levels if needed */

        .collected-attribute-row[data-level="0"] { padding-left: 1.5rem; } /* Base padding for top level */
        .collected-attribute-row[data-level="1"] { padding-left: 3.0rem; }
        .collected-attribute-row[data-level="2"] { padding-left: 4.5rem; }
        .collected-attribute-row[data-level="3"] { padding-left: 6.0rem; }
        .collected-attribute-row[data-level="4"] { padding-left: 7.5rem; }
        /* Add more levels if needed for collected data display */

        /* Message Box specific styles */
        .message-box-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }
        .message-box-content {
            background-color: white;
            padding: 2rem;
            border-radius: 0.5rem;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            width: 90%;
            max-width: 400px;
            text-align: center;
        }
    </style>
</head>
<body class="min-h-screen flex items-center justify-center p-4">

    <!-- Login Page Container -->
    <div id="login-page" class="bg-white p-8 rounded-lg shadow-xl w-full max-w-md">
        <h1 class="text-3xl font-bold text-center text-gray-800 mb-8">Login to CSI Portal</h1>
        <form id="login-form" class="space-y-6">
            <div>
                <label for="role-select" class="block text-sm font-medium text-gray-700 mb-2">Select Role:</label>
                <select id="role-select" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md shadow-sm">
                    <option value="">-- Select Role --</option>
                    <option value="developer">Developer</option>
                    <option value="editor">Editor</option>
                </select>
            </div>
            <div>
                <label for="username-input" class="block text-sm font-medium text-gray-700 mb-2">Username:</label>
                <input type="text" id="username-input" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Enter username">
            </div>
            <div>
                <label for="password-input" class="block text-sm font-medium text-gray-700 mb-2">Password:</label>
                <input type="password" id="password-input" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Enter password">
            </div>
            <button type="submit" class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                Login
            </button>
        </form>
        <p id="login-error-message" class="text-red-500 text-center mt-4 hidden"></p>
    </div>

    <!-- Main Application Container (Initially Hidden) -->
    <div id="main-app" class="bg-white p-8 rounded-lg shadow-xl w-full max-w-4xl hidden">
        <h1 class="text-3xl font-bold text-center text-gray-800 mb-8">CSI Division Attribute Portal</h1>

        <!-- Import/Export Section for CSI Hierarchy -->
        <div class="flex flex-col sm:flex-row justify-center gap-4 mb-8">
            <button id="import-excel-btn" class="px-6 py-3 bg-indigo-600 text-white font-semibold rounded-lg shadow-lg hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 transition-all duration-200 transform hover:scale-105">
                Import CSI Data from Excel
            </button>
            <input type="file" id="excel-file-input" accept=".xlsx, .xls" class="hidden">
            <button id="download-template-btn" class="px-6 py-3 bg-gray-600 text-white font-semibold rounded-lg shadow-lg hover:bg-gray-700 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2 transition-all duration-200 transform hover:scale-105">
                Download Import Template
            </button>
        </div>


        <!-- Selection Section -->
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
            <!-- Division Selection (Dropdown) -->
            <div>
                <label for="division-select" class="block text-sm font-medium text-gray-700 mb-2">CSI Division:</label>
                <select id="division-select" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md shadow-sm">
                    <option value="">Select Division</option>
                </select>
            </div>

            <!-- Section Selection (Dropdown) -->
            <div>
                <label for="section-select" class="block text-sm font-medium text-gray-700 mb-2">
                    Section:
                </label>
                <select id="section-select" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md shadow-sm" disabled>
                    <option value="">Select Section</option>
                </select>
            </div>

            <!-- CSI Section (Detailed) Selection (Dropdown) -->
            <div>
                <label for="subsection-select" class="block text-sm font-medium text-gray-700 mb-2">
                    CSI Section (Detailed):
                </label>
                <select id="subsection-select" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md shadow-sm" disabled>
                    <option value="">Select Detailed Section</option>
                </select>
            </div>

            <!-- CSI Item (Detailed) Selection (NEW Dropdown) -->
            <div>
                <label for="item-select" class="block text-sm font-medium text-gray-700 mb-2">
                    CSI Item (Detailed):
                </label>
                <select id="item-select" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md shadow-sm" disabled>
                    <option value="">Select Item</option>
                </select>
            </div>
        </div>

        <!-- Attribute Input Section -->
        <div id="attribute-input-section" class="border border-gray-200 p-6 rounded-lg mb-8 bg-gray-50 hidden">
            <h2 class="text-xl font-semibold text-gray-800 mb-4">Attributes for <span id="current-item-name" class="text-indigo-600"></span></h2>
            <div id="attributes-container" class="space-y-4">
                <!-- Attribute input fields will be added here dynamically -->
            </div>
            <div class="flex justify-end mt-6">
                <button id="save-all-attributes-btn" class="px-4 py-2 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-green-600 hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-green-500 transition ease-in-out duration-150">
                    Save All Attributes
                </button>
            </div>
        </div>

        <!-- Collected Data Display -->
        <div id="collected-data-section" class="hidden">
            <h2 class="text-xl font-semibold text-gray-800 mb-4">Collected Data</h2>
            <div class="overflow-x-auto rounded-lg shadow-md border border-gray-200">
                <table class="min-w-full divide-y divide-gray-200">
                    <thead class="bg-gray-100">
                        <tr>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Division</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Section</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">CSI Section (Detailed)</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">CSI Item (Detailed)</th> <!-- NEW COLUMN -->
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Attribute Name</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Standard Values</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Mandatory</th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Actions</th>
                        </tr>
                    </thead>
                    <tbody id="collected-data-body" class="bg-white divide-y divide-gray-200">
                        <!-- Collected data rows will be appended here -->
                    </tbody>
                </table>
            </div>
            <button id="export-excel-btn" class="mt-6 w-full px-6 py-3 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-purple-600 hover:bg-purple-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-purple-500 transition ease-in-out duration-150">
                Export to Excel
            </button>
        </div>

        <!-- Message Box for alerts -->
        <div id="message-box" class="message-box-overlay hidden">
            <div class="message-box-content">
                <p id="message-text" class="text-lg font-medium text-gray-800 mb-4"></p>
                <button id="message-box-ok-btn" class="px-4 py-2 bg-indigo-600 text-white rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">OK</button>
            </div>
        </div>

    </div>

    <!-- SheetJS CDN for Excel export -->
    <script src="https://unpkg.com/xlsx/dist/xlsx.full.min.js"></script>

    <script>
        // Initial CSI Data structure will be empty and populated via Excel import.
        // Structure:
        // {
        //   "Division 01 - General Requirements": {
        //     "Section 3 - Administrative Requirements": { // Section is now a single digit
        //       "Detailed Section 1 - Project Meetings": [ // Detailed Section is now a single digit
        //         "Item 01 31 19.13 - Preconstruction Meetings", // Item is XX YY ZZ.AA
        //         "Item 01 31 19.16 - Site Mobilization Meetings"
        //       ]
        //     }
        //   }
        // }
        let csiData = {};

        // DOM elements
        const loginPage = document.getElementById('login-page');
        const mainApp = document.getElementById('main-app');
        const loginForm = document.getElementById('login-form');
        const roleSelect = document.getElementById('role-select');
        const usernameInput = document.getElementById('username-input');
        const passwordInput = document.getElementById('password-input');
        const loginErrorMessage = document.getElementById('login-error-message');

        const divisionSelect = document.getElementById('division-select');
        const sectionSelect = document.getElementById('section-select');
        const subsectionSelect = document.getElementById('subsection-select'); // Now CSI Section (Detailed)
        const itemSelect = document.getElementById('item-select'); // NEW: CSI Item (Detailed)

        const attributeInputSection = document.getElementById('attribute-input-section');
        const currentItemNameSpan = document.getElementById('current-item-name'); // Updated from currentSubsectionNameSpan
        const attributesContainer = document.getElementById('attributes-container');
        const saveAllAttributesBtn = document.getElementById('save-all-attributes-btn');
        const collectedDataSection = document.getElementById('collected-data-section');
        const collectedDataBody = document.getElementById('collected-data-body');
        const exportExcelBtn = document.getElementById('export-excel-btn');
        const messageBox = document.getElementById('message-box');
        const messageText = document.getElementById('message-text');
        const messageBoxOkBtn = document.getElementById('message-box-ok-btn');

        // Excel Import/Export for CSI Hierarchy
        const importExcelBtn = document.getElementById('import-excel-btn');
        const excelFileInput = document.getElementById('excel-file-input');
        const downloadTemplateBtn = document.getElementById('download-template-btn');

        // Global variable to store collected attributes (session-based)
        let collectedData = [];
        let currentAttributeIdCounter = 0; // To generate unique IDs for new attributes
        let currentUserRole = null; // Stores the role of the logged-in user

        // User credentials (for demonstration purposes only, in a real app this would be server-side)
        const users = {
            developer: { username: 'ziadahmed', password: 'ziad123' },
            editor: { username: 'user1', password: 'user1' }
        };


        // Function to show a message box
        function showMessage(message) {
            messageText.textContent = message;
            messageBox.classList.remove('hidden');
        }

        // Function to populate dropdowns
        function populateDropdown(selectElement, options) {
            // Determine the default option text based on the select element's ID
            let defaultOptionText = '';
            if (selectElement.id === 'division-select') {
                defaultOptionText = 'Select Division';
            } else if (selectElement.id === 'section-select') {
                defaultOptionText = 'Select Section';
            } else if (selectElement.id === 'subsection-select') {
                defaultOptionText = 'Select Detailed Section';
            } else if (selectElement.id === 'item-select') { // NEW
                defaultOptionText = 'Select Item';
            }

            selectElement.innerHTML = `<option value="">${defaultOptionText}</option>`;
            options.forEach(option => {
                const opt = document.createElement('option');
                opt.value = option;
                opt.textContent = option;
                selectElement.appendChild(opt);
            });
        }

        /**
         * Renders the standard value input fields for a given attribute.
         * Ensures the "Add" button is always on the last row and all rows have "Remove" buttons.
         * @param {HTMLElement} standardValuesContainer - The container div for standard values.
         * @param {Array<Object>} valuesArray - An array of {value: string, unit: string} for existing values.
         */
        function renderStandardValueInputs(standardValuesContainer, valuesArray) {
            standardValuesContainer.innerHTML = ''; // Clear existing inputs

            // If no values, add one empty row with Add and Remove
            if (valuesArray.length === 0) {
                const newDiv = document.createElement('div');
                newDiv.className = 'flex gap-2 mt-2';
                newDiv.innerHTML = `
                    <input type="text" class="standard-value-input flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Value">
                    <input type="text" class="standard-unit-input w-24 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Unit">
                    <button type="button" class="add-value-btn px-3 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition ease-in-out duration-150">Add</button>
                    <button type="button" class="remove-value-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">Remove</button>
                `;
                standardValuesContainer.appendChild(newDiv);
                return;
            }

            // Render existing values
            valuesArray.forEach((sv, index) => {
                const newDiv = document.createElement('div');
                newDiv.className = 'flex gap-2 mt-2';
                const isLastRow = (index === valuesArray.length - 1);

                newDiv.innerHTML = `
                    <input type="text" class="standard-value-input flex-1 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Value" value="${sv.value}">
                    <input type="text" class="standard-unit-input w-24 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Unit" value="${sv.unit}">
                    ${isLastRow ? `<button type="button" class="add-value-btn px-3 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition ease-in-out duration-150">Add</button>` : ''}
                    <button type="button" class="remove-value-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">Remove</button>
                `;
                standardValuesContainer.appendChild(newDiv);
            });
        }

        /**
         * Renders an attribute input block, potentially with nested children.
         * @param {HTMLElement} parentContainer - The DOM element where this attribute block should be appended.
         * @param {number} level - The nesting level of this attribute (0 for top-level).
         * @param {Object} [attributeData] - Optional existing attribute data to pre-fill the form.
         */
        function renderAttributeInput(parentContainer, level, attributeData = null) {
            const attributeId = currentAttributeIdCounter++; // Unique ID for this attribute block

            const attributeRowDiv = document.createElement('div');
            attributeRowDiv.className = `attribute-input-row space-y-3 p-4 border border-gray-300 rounded-md bg-white mb-4`;
            attributeRowDiv.setAttribute('data-level', level);
            attributeRowDiv.setAttribute('data-attribute-id', attributeId); // Store unique ID

            // The remove button should ONLY be shown if level > 0 (i.e., it's a child attribute)
            const showRemoveButton = (level > 0);

            attributeRowDiv.innerHTML = `
                <div>
                    <label for="attribute-name-input-${attributeId}" class="block text-sm font-medium text-gray-700">Attribute Name:</label>
                    <input type="text" id="attribute-name-input-${attributeId}" class="attribute-name-input mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="e.g., Material Type" value="${attributeData ? attributeData.attributeName : ''}">
                </div>
                <div class="flex items-center">
                    <input type="checkbox" id="mandatory-checkbox-${attributeId}" class="mandatory-checkbox h-4 w-4 text-indigo-600 focus:ring-indigo-500 border-gray-300 rounded" ${attributeData && attributeData.isMandatory ? 'checked' : ''}>
                    <label for="mandatory-checkbox-${attributeId}" class="ml-2 block text-sm text-gray-900">Mandatory</label>
                </div>
                <div class="standard-values-section space-y-2" id="standard-values-container-${attributeId}">
                    <!-- Standard values will be rendered here by renderStandardValueInputs -->
                </div>
                <div class="flex justify-end gap-2 mt-4">
                    <button type="button" class="add-child-attribute-btn px-3 py-2 bg-indigo-500 text-white rounded-md hover:bg-indigo-600 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 transition ease-in-out duration-150">
                        Add Child Attribute
                    </button>
                    <!-- New Clear Inputs button -->
                    <button type="button" class="clear-attribute-inputs-btn px-3 py-2 bg-yellow-500 text-white rounded-md hover:bg-yellow-600 focus:outline-none focus:ring-2 focus:ring-yellow-500 focus:ring-offset-2 transition ease-in-out duration-150">
                        Clear Inputs
                    </button>
                    ${showRemoveButton ? `
                        <button type="button" class="remove-attribute-btn px-3 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 transition ease-in-out duration-150">
                            Remove Child Attribute
                        </button>
                    ` : ''}
                </div>
                <div class="child-attributes-container mt-4 space-y-4">
                    <!-- Child attributes will be rendered here -->
                </div>
            `;
            parentContainer.appendChild(attributeRowDiv);

            const standardValuesSection = attributeRowDiv.querySelector(`#standard-values-container-${attributeId}`);
            const childAttributesContainer = attributeRowDiv.querySelector('.child-attributes-container');

            // Initial render of standard values or hide if children exist
            if (attributeData && attributeData.children && attributeData.children.length > 0) {
                standardValuesSection.classList.add('hidden'); // Hide the standard values section
            } else {
                renderStandardValueInputs(standardValuesSection, attributeData ? attributeData.standardValues : []);
                standardValuesSection.classList.remove('hidden'); // Ensure it's visible if no children
            }

            // Recursively render child attributes if data exists
            if (attributeData && attributeData.children && attributeData.children.length > 0) {
                attributeData.children.forEach(childAttr => {
                    renderAttributeInput(childAttributesContainer, level + 1, childAttr);
                });
            }

            // Event Listeners for the newly created attribute block (using event delegation on the block itself)
            attributeRowDiv.addEventListener('click', (event) => {
                const target = event.target;
                const valuesContainer = attributeRowDiv.querySelector(`#standard-values-container-${attributeId}`); // Correctly scope valuesContainer

                // Add Standard Value
                if (target.classList.contains('add-value-btn')) {
                    // Collect current values from the DOM
                    const currentValues = [];
                    Array.from(valuesContainer.children).forEach(row => {
                        if (row.classList.contains('flex')) { // Check if it's an input row
                            currentValues.push({
                                value: row.querySelector('.standard-value-input').value.trim(),
                                unit: row.querySelector('.standard-unit-input').value.trim()
                            });
                        }
                    });
                    currentValues.push({ value: '', unit: '' }); // Add a new empty value
                    renderStandardValueInputs(valuesContainer, currentValues);
                }
                // Remove Standard Value
                if (target.classList.contains('remove-value-btn')) {
                    const rowToRemove = target.closest('.flex.gap-2.mt-2');
                    // Collect current values from the DOM, excluding the one to be removed
                    const currentValues = [];
                    Array.from(valuesContainer.children).forEach(row => {
                        if (row.classList.contains('flex') && row !== rowToRemove) {
                            currentValues.push({
                                value: row.querySelector('.standard-value-input').value.trim(),
                                unit: row.querySelector('.standard-unit-input').value.trim()
                            });
                        }
                    });
                    renderStandardValueInputs(valuesContainer, currentValues);
                }
                // Add Child Attribute
                if (target.classList.contains('add-child-attribute-btn')) {
                    const childContainer = attributeRowDiv.querySelector('.child-attributes-container');
                    renderAttributeInput(childContainer, level + 1); // Render new child

                    // Hide parent's standard values section if a child is added
                    const parentStandardValuesSection = attributeRowDiv.querySelector('.standard-values-section');
                    if (parentStandardValuesSection) {
                        parentStandardValuesSection.classList.add('hidden');
                    }
                }
                // Remove Attribute (this attribute block)
                if (target.classList.contains('remove-attribute-btn')) {
                    const attributeBlockToRemove = target.closest('.attribute-input-row');
                    if (attributeBlockToRemove) {
                        const parentChildContainer = attributeBlockToRemove.parentNode; // This is the .child-attributes-container
                        const parentAttributeRowDiv = parentChildContainer.closest('.attribute-input-row'); // This is the parent attribute's row

                        attributeBlockToRemove.remove(); // Remove the child attribute

                        // If there's a parent attribute, check if it now has no children
                        if (parentAttributeRowDiv) {
                            const remainingChildren = parentAttributeRowDiv.querySelector('.child-attributes-container').children.length;

                            // If parent now has no children, re-enable parent's standard value inputs
                            if (remainingChildren === 0) {
                                const parentStandardValuesSection = parentAttributeRowDiv.querySelector('.standard-values-section');
                                if (parentStandardValuesSection) {
                                    parentStandardValuesSection.classList.remove('hidden');
                                    renderStandardValueInputs(parentStandardValuesSection, [{value: '', unit: ''}]); // Add one empty value input
                                }
                            }
                        }
                    } else {
                        console.error('Could not find attribute block to remove.');
                    }
                }
                // Clear Inputs for this attribute block
                if (target.classList.contains('clear-attribute-inputs-btn')) {
                    const currentAttributeBlock = target.closest('.attribute-input-row');
                    if (currentAttributeBlock) {
                        currentAttributeBlock.querySelector('.attribute-name-input').value = '';
                        currentAttributeBlock.querySelector('.mandatory-checkbox').checked = false;
                        const standardValuesSection = currentAttributeBlock.querySelector('.standard-values-section');
                        if (standardValuesSection) {
                            standardValuesSection.classList.remove('hidden'); // Ensure it's visible
                            renderStandardValueInputs(standardValuesSection, []); // Render with empty array to clear
                        }
                        // Also clear any child attributes
                        const childContainer = currentAttributeBlock.querySelector('.child-attributes-container');
                        if (childContainer) {
                            childContainer.innerHTML = '';
                        }
                    }
                }
            });
        }


        /**
         * Collects attribute data from the dynamically created input fields.
         * This function is recursive to handle child attributes.
         * @param {HTMLElement} attributeElement - The DOM element representing an attribute row.
         * @returns {Object|null} The collected attribute object or null if invalid.
         */
        function collectAttributeData(attributeElement) {
            const attributeNameInput = attributeElement.querySelector('.attribute-name-input');
            const mandatoryCheckbox = attributeElement.querySelector('.mandatory-checkbox');
            const standardValuesContainer = attributeElement.querySelector('.standard-values-section');
            const childAttributeContainers = attributeElement.querySelector('.child-attributes-container');

            const attributeName = attributeNameInput.value.trim();
            const isMandatory = mandatoryCheckbox.checked;

            if (!attributeName) {
                return null;
            }

            const children = [];
            if (childAttributeContainers) {
                Array.from(childAttributeContainers.children).forEach(childElement => {
                    const childAttr = collectAttributeData(childElement);
                    if (childAttr) {
                        children.push(childAttr);
                    }
                });
            }

            const standardValues = [];
            // Only collect standard values if there are no children for this attribute
            if (children.length === 0 && standardValuesContainer && !standardValuesContainer.classList.contains('hidden')) {
                // Iterate over direct children of standardValuesContainer
                Array.from(standardValuesContainer.children).forEach(row => {
                    // Check if the child is an input row (has the 'flex' class)
                    if (row.classList.contains('flex')) {
                        const valueInput = row.querySelector('.standard-value-input');
                        const unitInput = row.querySelector('.standard-unit-input');
                        if (valueInput && unitInput) { // Ensure elements exist
                            const value = valueInput.value.trim();
                            const unit = unitInput.value.trim();
                            if (value) { // Only add if value is not empty
                                standardValues.push({ value, unit });
                            }
                        }
                    }
                });
            }

            return {
                attributeName: attributeName,
                standardValues: standardValues,
                isMandatory: isMandatory,
                children: children // Will be empty array if no children
            };
        }


        /**
         * Saves all attributes currently displayed in the input section.
         */
        function saveAllAttributes() {
            const currentDivisionName = divisionSelect.options[divisionSelect.selectedIndex].text;
            const currentSectionName = sectionSelect.options[sectionSelect.selectedIndex].text;
            const currentSubsectionName = subsectionSelect.options[subsectionSelect.selectedIndex].text; // CSI Section (Detailed)
            const currentItemName = itemSelect.options[itemSelect.selectedIndex].text; // NEW: CSI Item (Detailed)

            // Validate that a full hierarchy is selected/entered
            if (!currentDivisionName || currentDivisionName === 'Select Division' ||
                !currentSectionName || currentSectionName.includes('Select Section') ||
                !currentSubsectionName || currentSubsectionName.includes('Select Detailed Section') ||
                !currentItemName || currentItemName.includes('Select Item')) { // NEW validation
                showMessage("Please select a complete CSI Division, Section, Detailed Section, and Item before saving attributes.");
                return;
            }

            const attributesFromForm = [];
            Array.from(attributesContainer.children).forEach(attributeElement => {
                const attribute = collectAttributeData(attributeElement);
                if (attribute) {
                    // Add the hierarchy information to the top-level attributes
                    attribute.division = currentDivisionName;
                    attribute.section = currentSectionName;
                    attribute.subsection = currentSubsectionName;
                    attribute.item = currentItemName; // NEW
                    attribute.level = 0; // Ensure top-level attributes have level 0
                    attributesFromForm.push(attribute);
                }
            });

            if (attributesFromForm.length === 0) {
                showMessage("No valid attributes to save. Please enter at least one attribute name.");
                return;
            }

            // Instead of filtering and updating, simply add new entries
            attributesFromForm.forEach(newAttr => {
                collectedData.push(newAttr); // Add as a new entry
            });

            renderCollectedData(); // Re-render the table
            showMessage("All attributes saved successfully!");
            // Optionally, clear the input section after saving
            attributesContainer.innerHTML = '';
            renderAttributeInput(attributesContainer, 0); // Add a new blank top-level attribute
        }


        /**
         * Renders collected data in the table, handling nested attributes.
         * @param {Object} attribute - The attribute object to render (can be top-level or child).
         * @param {HTMLElement} parentBody - The tbody or parent element to append rows to.
         * @param {Object} parentHierarchy - The division, section, subsection, item info inherited from parent.
         * @param {number} level - The current nesting level for display indentation.
         * @param {number[]} path - Array representing the path (indices) to the current attribute for deletion.
         */
        function renderCollectedAttributeRow(attribute, parentBody, parentHierarchy, level, path) {
            const row = document.createElement('tr');
            row.className = 'collected-attribute-row border-b border-gray-100';
            row.setAttribute('data-level', level);

            // Format standard values for display
            const standardValuesDisplay = attribute.standardValues.map(sv => {
                return `${sv.value}${sv.unit ? ` (${sv.unit})` : ''}`;
            }).join(', ');

            // Only show hierarchy for top-level attributes
            const displayDivision = level === 0 ? parentHierarchy.division : '';
            const displaySection = level === 0 ? parentHierarchy.section : '';
            const displaySubsection = level === 0 ? parentHierarchy.subsection : '';
            const displayItem = level === 0 ? parentHierarchy.item : ''; // NEW

            row.innerHTML = `
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${displayDivision}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${displaySection}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${displaySubsection}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${displayItem}</td> <!-- NEW COLUMN -->
                <td class="px-6 py-4 text-sm text-gray-900" style="padding-left: ${1.5 + level * 1.5}rem;">${attribute.attributeName}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${standardValuesDisplay}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${attribute.isMandatory ? 'Yes' : 'No'}</td>
                <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <button type="button" class="delete-attribute-btn text-red-600 hover:text-red-900 ml-4" data-path="${JSON.stringify(path)}">Delete</button>
                </td>
            `;
            parentBody.appendChild(row);

            // Recursively render children
            if (attribute.children && attribute.children.length > 0) {
                attribute.children.forEach((childAttr, childIndex) => {
                    renderCollectedAttributeRow(childAttr, parentBody, parentHierarchy, level + 1, [...path, childIndex]);
                });
            }
        }

        // Main function to render all collected data
        function renderCollectedData() {
            collectedDataBody.innerHTML = ''; // Clear existing table rows
            if (collectedData.length === 0) {
                collectedDataSection.classList.add('hidden');
                return;
            } else {
                collectedDataSection.classList.remove('hidden');
            }

            collectedData.forEach((item, index) => {
                const hierarchyInfo = {
                    division: item.division,
                    section: item.section,
                    subsection: item.subsection,
                    item: item.item // NEW
                };
                renderCollectedAttributeRow(item, collectedDataBody, hierarchyInfo, item.level || 0, [index]);
            });
        }

        /**
         * Deletes an attribute based on its path in the collectedData array.
         * @param {number[]} path - An array of indices representing the path to the attribute (e.g., [0, 1] for the second child of the first top-level attribute).
         */
        function deleteAttribute(path) {
            let currentLevel = collectedData;
            let targetAttribute = null;
            let parentArray = null;
            let indexInParent = -1;

            for (let i = 0; i < path.length; i++) {
                const index = path[i];
                if (!currentLevel || index === undefined || index < 0 || index >= currentLevel.length) {
                    console.error("Invalid path for deletion:", path);
                    showMessage("Error: Could not find attribute to delete.");
                    return;
                }

                if (i === path.length - 1) { // Last element in path is the target
                    targetAttribute = currentLevel[index];
                    parentArray = currentLevel;
                    indexInParent = index;
                } else {
                    currentLevel = currentLevel[index].children;
                }
            }

            if (parentArray && indexInParent !== -1) {
                parentArray.splice(indexInParent, 1);
                renderCollectedData();
                showMessage("Attribute deleted successfully!");
            } else {
                showMessage("Error: Could not find attribute to delete.");
            }
        }

        /**
         * Recursively flattens attributes for Excel export, including full hierarchy and parent details.
         * @param {Array} attributes - The array of attributes (current level) to process.
         * @param {Object} hierarchyInfo - Object containing full division, section, subsection, item names.
         * @param {Object|null} parentAttributeDetails - Object with parent's name and standard values, or null if top-level.
         * @param {number} level - The current nesting level.
         * @param {Array<Array>} dataToExport - The array to push flattened rows into.
         */
        function flattenAttributesForExport(attributes, hierarchyInfo, parentAttributeDetails, level, dataToExport) {
            attributes.forEach(attr => {
                const currentStandardValues = attr.standardValues.map(sv => `${sv.value}${sv.unit ? ` (${sv.unit})` : ''}`).join(', ');
                const parentAttributeName = parentAttributeDetails ? parentAttributeDetails.attributeName : '';
                const parentStandardValues = parentAttributeDetails ? parentAttributeDetails.standardValues.map(sv => `${sv.value}${sv.unit ? ` (${sv.unit})` : ''}`).join(', ') : '';

                // Extract codes and descriptions from the full names in hierarchyInfo
                const divMatch = hierarchyInfo.division.match(/Division (\d{2}) - (.*)/);
                const divCode = divMatch ? divMatch[1] : '';
                const divDesc = divMatch ? divMatch[2] : '';

                const secMatch = hierarchyInfo.section.match(/Section (\d) - (.*)/);
                const secCode = secMatch ? secMatch[1] : '';
                const secDesc = secMatch ? secMatch[2] : '';

                const detailedSecMatch = hierarchyInfo.subsection.match(/Detailed Section (\d) - (.*)/);
                const detailedSecCode = detailedSecMatch ? detailedSecMatch[1] : '';
                const detailedSecDesc = detailedSecMatch ? detailedSecMatch[2] : '';

                // The item name is like "Item 03 23 19.00 - Description"
                const itemMatch = hierarchyInfo.item.match(/Item \d{2} (\d{2}) (\d{2}(\.\d{2})?) - (.*)/);
                const itemCodePart = itemMatch ? itemMatch[3] : ''; // The XX.YY part
                const itemDesc = itemMatch ? itemMatch[5] : ''; // Description

                const rowData = [
                    divCode,
                    divDesc,
                    secCode,
                    secDesc,
                    detailedSecCode,
                    detailedSecDesc,
                    itemCodePart, // Export only the XX.YY part
                    itemDesc,
                    parentAttributeName,
                    parentStandardValues,
                    level, // Attribute Level
                    attr.attributeName,
                    currentStandardValues,
                    attr.isMandatory ? 'Yes' : 'No'
                ];
                dataToExport.push(rowData);

                if (attr.children && attr.children.length > 0) {
                    // For children, the current attribute becomes the parent
                    flattenAttributesForExport(attr.children, hierarchyInfo, {
                        attributeName: attr.attributeName,
                        standardValues: attr.standardValues // Pass full standard values for parent
                    }, level + 1, dataToExport);
                }
            });
        }

        // --- Helper Functions for CSI Code Parsing (Adjusted for new single-digit section/detailed section) ---

        /**
         * Extracts the 2-digit division code from a full division name string.
         * E.g., "Division 00 - Procurement..." -> "00"
         * @param {string} divisionName The full division name.
         * @returns {string} The 2-digit division code, or empty string if not found.
         */
        function getDivisionCode(divisionName) {
            const match = divisionName.match(/Division (\d{2})/);
            return match ? match[1] : '';
        }

        /**
         * Extracts the single digit section code from a full section name string.
         * E.g., "Section 3 - Solicitations" -> "3"
         * @param {string} sectionName The full section name.
         * @returns {string} The single digit section code, or empty string if not found.
         */
        function getSectionCode(sectionName) {
            const match = sectionName.match(/Section (\d) -/);
            return match ? match[1] : '';
        }

        /**
         * Extracts the single digit detailed section code from a full detailed section name string.
         * E.g., "Detailed Section 1 - Project Meetings" -> "1"
         * @param {string} detailedSectionName The full detailed section name.
         * @returns {string} The single digit detailed section code, or empty string if not found.
         */
        function getDetailedSectionCode(detailedSectionName) {
            const match = detailedSectionName.match(/Detailed Section (\d) -/);
            return match ? match[1] : '';
        }

        /**
         * Extracts the full ZZ.AA item code from a full item name string.
         * E.g., "Item 01 31 19.13 - Preconstruction Meetings" -> "19.13"
         * @param {string} itemName The full item name.
         * @returns {string} The ZZ.AA item code, or empty string if not found.
         */
        function getItemCodeFromFullItemName(itemName) {
            // Updated regex to match the new format: "Item XX YZ WW.VV - Description"
            const match = itemName.match(/Item \d{2} (\d{2}) (\d{2}(\.\d{2})?) - (.*)/);
            return match ? match[3] : ''; // Capture group 3 is the XX.YY part
        }

        /**
         * Constructs a full division name string.
         * E.g., "01", "General Requirements" -> "Division 01 - General Requirements"
         * @param {string} code The 2-digit division code.
         * @param {string} description The division description.
         * @returns {string} The formatted division name.
         */
        function constructFullDivisionName(code, description) {
            return `Division ${code} - ${description}`;
        }

        /**
         * Constructs a full section name string.
         * E.g., "3", "Administrative Requirements" -> "Section 3 - Administrative Requirements"
         * @param {string} code The single-digit section code.
         * @param {string} description The section description.
         * @returns {string} The formatted section name.
         */
        function constructFullSectionName(code, description) {
            return `Section ${code} - ${description}`;
        }

        /**
         * Constructs a full detailed section name string.
         * E.g., "1", "Project Meetings" -> "Detailed Section 1 - Project Meetings"
         * @param {string} code The single-digit detailed section code.
         * @param {string} description The detailed section description.
         * @returns {string} The formatted detailed section name.
         */
        function constructFullDetailedSectionName(code, description) {
            return `Detailed Section ${code} - ${description}`;
        }

        /**
         * Constructs a full item name string.
         * E.g., "01", "3", "1", "19.13", "Preconstruction Meetings" -> "Item 01 31 19.13 - Preconstruction Meetings"
         * @param {string} divCode The 2-digit division code.
         * @param {string} secCode The single-digit section code.
         * @param {string} detailedSecCode The single-digit detailed section code.
         * @param {string} itemCode The XX.YY item code.
         * @param {string} description The item description.
         * @returns {string} The formatted item name.
         */
        function constructFullItemName(divCode, secCode, detailedSecCode, itemCode, description) {
            // Concatenate secCode and detailedSecCode for the full item code string
            const combinedSecDetailedCode = `${secCode}${detailedSecCode}`;
            return `Item ${divCode} ${combinedSecDetailedCode} ${itemCode} - ${description}`;
        }


        // --- Excel Import/Export Functions for CSI Hierarchy ---

        /**
         * Handles importing CSI data from an Excel file.
         */
        function handleImportExcel(event) {
            if (typeof XLSX === 'undefined') {
                showMessage('XLSX library not loaded. Please try again.');
                return;
            }

            const file = event.target.files[0];
            if (!file) {
                return;
            }

            const reader = new FileReader();
            reader.onload = (e) => {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, { type: 'array' });
                const firstSheetName = workbook.SheetNames[0];
                const worksheet = workbook.Sheets[firstSheetName];
                const json = XLSX.utils.sheet_to_json(worksheet, { header: 1 }); // Get data as array of arrays

                if (json.length < 1) {
                    showMessage("Excel file is empty or has no data rows.");
                    return;
                }

                const headers = json[0]; // First row is headers
                // Expected headers for import
                const expectedHeaders = [
                    'CSI Division Code', 'CSI Division Description',
                    'CSI Section Code', 'CSI Section Description',
                    'CSI Detailed Section Code', 'CSI Detailed Section Description',
                    'CSI Item Code', 'CSI Item Description'
                ];

                // Validate headers
                const missingHeaders = expectedHeaders.filter(header => !headers.includes(header));
                if (missingHeaders.length > 0) {
                    showMessage(`Missing required Excel column headers: ${missingHeaders.join(', ')}. Please use the provided template.`);
                    return;
                }

                const newCsiData = {};

                // Map headers to their column indices for easier access
                const headerMap = {};
                headers.forEach((header, index) => {
                    headerMap[header] = index;
                });

                // Start from the second row (index 1) for data
                for (let i = 1; i < json.length; i++) {
                    const row = json[i];

                    const divCode = String(row[headerMap['CSI Division Code']]).padStart(2, '0') || '';
                    const divDesc = row[headerMap['CSI Division Description']] || '';
                    const secCode = String(row[headerMap['CSI Section Code']]) || ''; // Expected single digit
                    const secDesc = row[headerMap['CSI Section Description']] || '';
                    const detailedSecCode = String(row[headerMap['CSI Detailed Section Code']]) || ''; // Expected single digit
                    const detailedSecDesc = row[headerMap['CSI Detailed Section Description']] || '';
                    const itemCode = String(row[headerMap['CSI Item Code']]) || ''; // Expected XX.YY
                    const itemDesc = row[headerMap['CSI Item Description']] || '';

                    // Validate codes
                    if (!/^\d{2}$/.test(divCode)) {
                        showMessage(`Invalid CSI Division Code format in row ${i + 1}: "${divCode}". Expected 2 digits.`);
                        return;
                    }
                    if (secCode && !/^\d{1}$/.test(secCode)) {
                        showMessage(`Invalid CSI Section Code format in row ${i + 1}: "${secCode}". Expected 1 digit.`);
                        return;
                    }
                    if (detailedSecCode && !/^\d{1}$/.test(detailedSecCode)) {
                        showMessage(`Invalid CSI Detailed Section Code format in row ${i + 1}: "${detailedSecCode}". Expected 1 digit.`);
                        return;
                    }
                    if (itemCode && !/^\d{2}(\.\d{2})?$/.test(itemCode)) { // Allows XX or XX.YY
                        showMessage(`Invalid CSI Item Code format in row ${i + 1}: "${itemCode}". Expected XX or XX.YY.`);
                        return;
                    }


                    const fullDivisionName = constructFullDivisionName(divCode, divDesc);
                    if (!newCsiData[fullDivisionName]) {
                        newCsiData[fullDivisionName] = {};
                    }

                    if (secCode && secDesc) {
                        const fullSectionName = constructFullSectionName(secCode, secDesc);
                        if (!newCsiData[fullDivisionName][fullSectionName]) {
                            newCsiData[fullDivisionName][fullSectionName] = {};
                        }

                        if (detailedSecCode && detailedSecDesc) {
                            const fullDetailedSectionName = constructFullDetailedSectionName(detailedSecCode, detailedSecDesc);
                            if (!newCsiData[fullDivisionName][fullSectionName][fullDetailedSectionName]) {
                                newCsiData[fullDivisionName][fullSectionName][fullDetailedSectionName] = [];
                            }

                            if (itemCode && itemDesc) {
                                // Use the updated constructFullItemName to get the desired format
                                const fullItemName = constructFullItemName(divCode, secCode, detailedSecCode, itemCode, itemDesc);
                                // Add item only if it's not a duplicate within the same detailed section
                                if (!newCsiData[fullDivisionName][fullSectionName][fullDetailedSectionName].includes(fullItemName)) {
                                    newCsiData[fullDivisionName][fullSectionName][fullDetailedSectionName].push(fullItemName);
                                }
                            }
                        }
                    }
                }

                csiData = newCsiData; // Update the global csiData object
                populateDropdownsFromCsiData(); // Re-populate all dropdowns
                showMessage("CSI data imported successfully!");
            };
            reader.readAsArrayBuffer(file);
        }

        /**
         * Downloads an Excel template with the required column headers for CSI hierarchy import.
         */
        function handleDownloadTemplate() {
            if (typeof XLSX === 'undefined') {
                showMessage('XLSX library not loaded. Please try again.');
                return;
            }

            const templateHeaders = [
                'CSI Division Code', 'CSI Division Description',
                'CSI Section Code', 'CSI Section Description',
                'CSI Detailed Section Code', 'CSI Detailed Section Description',
                'CSI Item Code', 'CSI Item Description'
            ];

            const ws = XLSX.utils.aoa_to_sheet([templateHeaders]);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "CSI Import Template");
            XLSX.writeFile(wb, "CSI_Import_Template.xlsx");
            showMessage("CSI Import Template downloaded successfully!");
        }


        // --- Main Application Initialization and Event Listeners ---

        /**
         * Populates all dropdowns based on the current csiData.
         * This function is called after import or on initial load.
         */
        function populateDropdownsFromCsiData() {
            populateDropdown(divisionSelect, Object.keys(csiData));
            // Reset other dropdowns and disable them until a selection is made
            sectionSelect.value = '';
            subsectionSelect.value = '';
            itemSelect.value = '';
            sectionSelect.disabled = true;
            subsectionSelect.disabled = true;
            itemSelect.disabled = true;

            // Clear attribute input section and collected data
            attributeInputSection.classList.add('hidden');
            currentItemNameSpan.textContent = '';
            attributesContainer.innerHTML = '';
            collectedData = []; // Clear collected data on new hierarchy load
            renderCollectedData();
        }

        /**
         * Initializes the main CSI application view after successful login.
         * Applies role-based access control to modification buttons.
         */
        function initializeCSIApp() {
            // Hide login, show main app
            loginPage.classList.add('hidden');
            mainApp.classList.remove('hidden');

            // Populate Division dropdown (initially empty or with imported data)
            populateDropdownsFromCsiData();

            // Event listener for Division selection change
            divisionSelect.addEventListener('change', () => {
                const selectedDivision = divisionSelect.value;
                sectionSelect.value = '';
                subsectionSelect.value = '';
                itemSelect.value = '';
                sectionSelect.disabled = true;
                subsectionSelect.disabled = true;
                itemSelect.disabled = true;

                attributeInputSection.classList.add('hidden');
                currentItemNameSpan.textContent = '';
                attributesContainer.innerHTML = ''; // Clear attribute inputs

                if (selectedDivision && csiData[selectedDivision]) {
                    populateDropdown(sectionSelect, Object.keys(csiData[selectedDivision]));
                    sectionSelect.disabled = false;
                }
                collectedData = []; // Clear collected data when hierarchy selection changes
                renderCollectedData();
            });

            // Event listener for Section selection change
            sectionSelect.addEventListener('change', () => {
                const selectedDivision = divisionSelect.value;
                const selectedSection = sectionSelect.value;
                subsectionSelect.value = '';
                itemSelect.value = '';
                subsectionSelect.disabled = true;
                itemSelect.disabled = true;

                attributeInputSection.classList.add('hidden');
                currentItemNameSpan.textContent = '';
                attributesContainer.innerHTML = '';

                if (selectedSection && csiData[selectedDivision] && csiData[selectedDivision][selectedSection]) {
                    populateDropdown(subsectionSelect, Object.keys(csiData[selectedDivision][selectedSection]));
                    subsectionSelect.disabled = false;
                }

                attributesContainer.innerHTML = '';
                currentAttributeIdCounter = 0;
                renderAttributeInput(attributesContainer, 0);
                collectedData = []; // Clear collected data when hierarchy selection changes
                renderCollectedData();
            });

            // Event listener for CSI Section (Detailed) selection change
            subsectionSelect.addEventListener('change', () => {
                const selectedDivision = divisionSelect.value;
                const selectedSection = sectionSelect.value;
                const selectedSubsection = subsectionSelect.value;
                itemSelect.value = '';
                itemSelect.disabled = true;

                attributeInputSection.classList.add('hidden');
                currentItemNameSpan.textContent = '';
                attributesContainer.innerHTML = '';

                if (selectedSubsection && csiData[selectedDivision] && csiData[selectedDivision][selectedSection] && csiData[selectedDivision][selectedSection][selectedSubsection]) {
                    populateDropdown(itemSelect, csiData[selectedDivision][selectedSection][selectedSubsection]);
                    itemSelect.disabled = false;
                }

                attributesContainer.innerHTML = '';
                currentAttributeIdCounter = 0;
                renderAttributeInput(attributesContainer, 0);
                collectedData = []; // Clear collected data when hierarchy selection changes
                renderCollectedData();
            });

            // Event listener for CSI Item (Detailed) selection change (NEW)
            itemSelect.addEventListener('change', () => {
                const selectedDivision = divisionSelect.value;
                const selectedSection = sectionSelect.value;
                const selectedSubsection = subsectionSelect.value;
                const selectedItem = itemSelect.value;

                let itemDisplayName = selectedItem;

                if (selectedItem) {
                    attributeInputSection.classList.remove('hidden');
                    currentItemNameSpan.textContent = itemDisplayName;
                    attributesContainer.innerHTML = '';
                    currentAttributeIdCounter = 0;

                    // Filter existing attributes for the selected hierarchy to pre-populate the form
                    const existingAttributesForItem = collectedData.filter(item =>
                        item.division === divisionSelect.options[divisionSelect.selectedIndex].text &&
                        item.section === sectionSelect.options[sectionSelect.selectedIndex].text &&
                        item.subsection === subsectionSelect.options[subsectionSelect.selectedIndex].text &&
                        item.item === selectedItem
                    );

                    if (existingAttributesForItem.length > 0) {
                        existingAttributesForItem.forEach(attr => {
                            renderAttributeInput(attributesContainer, 0, attr);
                        });
                    } else {
                        renderAttributeInput(attributesContainer, 0); // Start with a blank one if none exist
                    }

                } else {
                    attributeInputSection.classList.add('hidden');
                    currentItemNameSpan.textContent = '';
                    attributesContainer.innerHTML = '';
                }
                renderCollectedData();
            });

            // Event listeners for Excel Import/Export for CSI Hierarchy
            importExcelBtn.addEventListener('click', () => {
                // Only developers can import new CSI hierarchy data
                if (currentUserRole !== 'developer') {
                    showMessage("You do not have permission to import CSI data.");
                    return;
                }
                excelFileInput.click(); // Trigger the hidden file input click
            });
            excelFileInput.addEventListener('change', handleImportExcel);
            downloadTemplateBtn.addEventListener('click', handleDownloadTemplate);


            // Event listener for "Save All Attributes" button
            saveAllAttributesBtn.addEventListener('click', saveAllAttributes);

            // Event listener for message box OK button
            messageBoxOkBtn.addEventListener('click', () => {
                messageBox.classList.add('hidden');
            });

            // Event listener for Export to Excel button (for collected attributes)
            exportExcelBtn.addEventListener('click', () => {
                if (typeof XLSX === 'undefined') {
                    showMessage('XLSX library not loaded. Please try again.');
                    return;
                }

                if (collectedData.length === 0) {
                    showMessage('No data to export.');
                    return;
                }

                const dataToExport = [];
                dataToExport.push([
                    'CSI Division Code', 'CSI Division Description',
                    'CSI Section Code', 'CSI Section Description',
                    'CSI Detailed Section Code', 'CSI Detailed Section Description',
                    'CSI Item Code', 'CSI Item Description',
                    'Parent Attribute Name', 'Parent Standard Values',
                    'Attribute Level', 'Attribute Name', 'Standard Values', 'Mandatory'
                ]);

                collectedData.forEach(item => {
                    const hierarchyInfo = {
                        division: item.division,
                        section: item.section,
                        subsection: item.subsection,
                        item: item.item
                    };
                    flattenAttributesForExport([item], hierarchyInfo, null, item.level || 0, dataToExport);
                });

                let fileItemName = '';
                if (itemSelect.value) {
                    fileItemName = itemSelect.value;
                } else if (subsectionSelect.value) {
                    fileItemName = subsectionSelect.value;
                } else {
                    fileItemName = 'All_Collected_Data';
                }

                const fileName = `${fileItemName.replace(/[^a-zA-Z0-9-_ ]/g, '')}_CSI_Attributes.xlsx`;

                const ws = XLSX.utils.aoa_to_sheet(dataToExport);
                const wb = XLSX.utils.book_new();
                XLSX.utils.book_append_sheet(wb, ws, "CSI Attributes");

                XLSX.writeFile(wb, fileName);
                showMessage("Data exported to Excel successfully!");
            });

            // Event listener for delete buttons on the collected data table (using event delegation)
            collectedDataBody.addEventListener('click', (event) => {
                if (event.target.classList.contains('delete-attribute-btn')) {
                    const pathString = event.target.dataset.path;
                    if (pathString) {
                        const path = JSON.parse(pathString);
                        deleteAttribute(path);
                    }
                }
            });

            // Initial state: Show login page on DOMContentLoaded
            document.addEventListener('DOMContentLoaded', () => {
                loginPage.classList.remove('hidden');
                mainApp.classList.add('hidden');
            });
        }

        // Login form submission handler
        loginForm.addEventListener('submit', (event) => {
            event.preventDefault(); // Prevent default form submission and page reload

            const selectedRole = roleSelect.value;
            const username = usernameInput.value;
            const password = passwordInput.value;

            const user = users[selectedRole];

            if (user && user.username === username && user.password === password) {
                currentUserRole = selectedRole;
                loginErrorMessage.classList.add('hidden'); // Hide error message on success
                initializeCSIApp(); // Initialize the main application
            } else {
                loginErrorMessage.textContent = 'Invalid role, username, or password.';
                loginErrorMessage.classList.remove('hidden');
            }
        });
    </script>
</body>
</html>
