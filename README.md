<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DabaoProtein Admin</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-storage-compat.js"></script>
    <style>
        .sidebar-link.active {
            background-color: #4f46e5;
            color: white;
        }
        .sidebar-link:hover:not(.active) {
            background-color: #e5e7eb;
        }
        .spinner {
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="bg-gray-100 font-sans">
    <!-- Login Screen -->
    <div id="loginScreen" class="fixed inset-0 flex items-center justify-center bg-gray-900 bg-opacity-90 z-50">
        <div class="bg-white p-8 rounded-lg shadow-xl w-full max-w-md">
            <div class="flex justify-center mb-6">
                <img src="https://dabaoprotein.netlify.app/logo.png" alt="DabaoProtein Logo" class="h-12">
            </div>
            <h2 class="text-2xl font-bold text-center text-gray-800 mb-6">Admin Login</h2>
            <form id="loginForm" class="space-y-4">
                <div>
                    <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
                    <input type="email" id="email" name="email" 
                           class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" 
                           placeholder="Enter your email" required>
                </div>
                <div>
                    <label for="password" class="block text-sm font-medium text-gray-700">Password</label>
                    <input type="password" id="password" name="password" 
                           class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" 
                           placeholder="Enter your password" required>
                </div>
                <div>
                    <button type="submit" id="loginBtn" class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                        Sign In
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- Admin Panel -->
    <div id="adminPanel" class="hidden">
        <!-- Sidebar -->
        <div class="fixed inset-y-0 left-0 w-64 bg-gray-800 text-white shadow-lg">
            <div class="flex items-center justify-center h-16 px-4 bg-gray-900">
                <img src="https://dabaoprotein.netlify.app/logo.png" alt="DabaoProtein Logo" class="h-10">
            </div>
            <nav class="mt-6">
                <div class="px-4 py-2 text-xs font-semibold text-gray-400 uppercase tracking-wider">
                    Main
                </div>
                <a href="#" id="dashboardLink" class="sidebar-link flex items-center px-4 py-3 text-sm font-medium text-white bg-indigo-600 rounded-lg mx-2">
                    <i class="fas fa-tachometer-alt mr-3"></i>
                    Dashboard
                </a>
                <a href="#" id="productsLink" class="sidebar-link flex items-center px-4 py-3 text-sm font-medium text-gray-300 hover:text-white hover:bg-gray-700 rounded-lg mx-2">
                    <i class="fas fa-boxes mr-3"></i>
                    Products
                </a>
                <a href="#" id="settingsLink" class="sidebar-link flex items-center px-4 py-3 text-sm font-medium text-gray-300 hover:text-white hover:bg-gray-700 rounded-lg mx-2">
                    <i class="fas fa-cog mr-3"></i>
                    Settings
                </a>
            </nav>
            <div class="absolute bottom-0 w-full px-4 py-3 bg-gray-900">
                <button id="logoutBtn" class="flex items-center text-sm font-medium text-gray-300 hover:text-white">
                    <i class="fas fa-sign-out-alt mr-3"></i>
                    Logout
                </button>
            </div>
        </div>

        <!-- Main Content -->
        <div class="ml-64">
            <!-- Top Navigation -->
            <header class="bg-white shadow-sm">
                <div class="flex justify-between items-center px-6 py-4">
                    <h1 class="text-xl font-semibold text-gray-800" id="pageTitle">Dashboard</h1>
                    <div class="flex items-center space-x-4">
                        <div class="relative">
                            <button id="profileBtn" class="flex items-center space-x-2 focus:outline-none">
                                <img id="adminAvatar" class="h-8 w-8 rounded-full" src="https://via.placeholder.com/32" alt="Admin profile">
                                <span id="adminName" class="text-sm font-medium text-gray-700">Admin</span>
                            </button>
                        </div>
                    </div>
                </div>
            </header>

            <!-- Dashboard Content -->
            <main class="p-6">
                <!-- Dashboard View -->
                <div id="dashboardView">
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-6">
                        <div class="bg-white p-6 rounded-lg shadow">
                            <div class="flex items-center justify-between">
                                <div>
                                    <p class="text-sm font-medium text-gray-500">Total Products</p>
                                    <p class="text-2xl font-semibold text-gray-800" id="totalProducts">0</p>
                                </div>
                                <div class="p-3 rounded-full bg-indigo-100 text-indigo-600">
                                    <i class="fas fa-box-open text-xl"></i>
                                </div>
                            </div>
                        </div>
                        <div class="bg-white p-6 rounded-lg shadow">
                            <div class="flex items-center justify-between">
                                <div>
                                    <p class="text-sm font-medium text-gray-500">Active Products</p>
                                    <p class="text-2xl font-semibold text-gray-800" id="activeProducts">0</p>
                                </div>
                                <div class="p-3 rounded-full bg-green-100 text-green-600">
                                    <i class="fas fa-check-circle text-xl"></i>
                                </div>
                            </div>
                        </div>
                        <div class="bg-white p-6 rounded-lg shadow">
                            <div class="flex items-center justify-between">
                                <div>
                                    <p class="text-sm font-medium text-gray-500">Out of Stock</p>
                                    <p class="text-2xl font-semibold text-gray-800" id="outOfStockProducts">0</p>
                                </div>
                                <div class="p-3 rounded-full bg-red-100 text-red-600">
                                    <i class="fas fa-times-circle text-xl"></i>
                                </div>
                            </div>
                        </div>
                        <div class="bg-white p-6 rounded-lg shadow">
                            <div class="flex items-center justify-between">
                                <div>
                                    <p class="text-sm font-medium text-gray-500">Categories</p>
                                    <p class="text-2xl font-semibold text-gray-800" id="totalCategories">4</p>
                                </div>
                                <div class="p-3 rounded-full bg-yellow-100 text-yellow-600">
                                    <i class="fas fa-tags text-xl"></i>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                        <div class="bg-white p-6 rounded-lg shadow">
                            <div class="flex items-center justify-between mb-4">
                                <h2 class="text-lg font-semibold text-gray-800">Recent Products</h2>
                                <a href="#" id="viewAllProducts" class="text-sm text-indigo-600 hover:text-indigo-500">View All</a>
                            </div>
                            <div class="overflow-x-auto">
                                <table class="min-w-full divide-y divide-gray-200">
                                    <thead class="bg-gray-50">
                                        <tr>
                                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Image</th>
                                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Name</th>
                                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Price</th>
                                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                                        </tr>
                                    </thead>
                                    <tbody id="recentProductsBody" class="bg-white divide-y divide-gray-200">
                                        <!-- Products will be loaded here -->
                                    </tbody>
                                </table>
                            </div>
                        </div>
                        <div class="bg-white p-6 rounded-lg shadow">
                            <div class="flex items-center justify-between mb-4">
                                <h2 class="text-lg font-semibold text-gray-800">Quick Actions</h2>
                            </div>
                            <div class="grid grid-cols-2 gap-4">
                                <button id="addProductQuickBtn" class="p-4 bg-indigo-100 text-indigo-700 rounded-lg hover:bg-indigo-200 transition">
                                    <i class="fas fa-plus-circle text-xl mb-2"></i>
                                    <p>Add Product</p>
                                </button>
                                <button id="manageSettingsQuickBtn" class="p-4 bg-green-100 text-green-700 rounded-lg hover:bg-green-200 transition">
                                    <i class="fas fa-cog text-xl mb-2"></i>
                                    <p>Manage Settings</p>
                                </button>
                                <button id="viewProductsQuickBtn" class="p-4 bg-blue-100 text-blue-700 rounded-lg hover:bg-blue-200 transition">
                                    <i class="fas fa-boxes text-xl mb-2"></i>
                                    <p>View Products</p>
                                </button>
                                <button id="refreshDataQuickBtn" class="p-4 bg-purple-100 text-purple-700 rounded-lg hover:bg-purple-200 transition">
                                    <i class="fas fa-sync-alt text-xl mb-2"></i>
                                    <p>Refresh Data</p>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Products View -->
                <div id="productsView" class="hidden">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-semibold text-gray-800">Product Management</h2>
                        <button id="addProductBtn" class="px-4 py-2 bg-indigo-600 text-white rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2">
                            <i class="fas fa-plus mr-2"></i> Add Product
                        </button>
                    </div>

                    <!-- Product Table -->
                    <div class="bg-white shadow rounded-lg overflow-hidden">
                        <div class="px-6 py-4 border-b border-gray-200 flex justify-between items-center">
                            <div class="flex items-center">
                                <div class="relative">
                                    <select id="productCategoryFilter" class="appearance-none bg-gray-100 border border-gray-300 rounded-md pl-3 pr-8 py-2 text-sm focus:outline-none focus:ring-1 focus:ring-indigo-500">
                                        <option value="all">All Categories</option>
                                        <option value="protein">Protein</option>
                                        <option value="preworkout">Pre-Workout</option>
                                        <option value="vitamins">Vitamins</option>
                                        <option value="accessories">Accessories</option>
                                    </select>
                                    <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                                        <i class="fas fa-chevron-down text-xs"></i>
                                    </div>
                                </div>
                            </div>
                            <div class="relative">
                                <input type="text" id="productSearch" placeholder="Search products..." class="pl-10 pr-4 py-2 border border-gray-300 rounded-md text-sm focus:outline-none focus:ring-1 focus:ring-indigo-500">
                                <div class="absolute left-3 top-2.5 text-gray-400">
                                    <i class="fas fa-search"></i>
                                </div>
                            </div>
                        </div>
                        <div class="overflow-x-auto">
                            <table class="min-w-full divide-y divide-gray-200">
                                <thead class="bg-gray-50">
                                    <tr>
                                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Image</th>
                                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Name</th>
                                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Category</th>
                                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Price</th>
                                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Stock</th>
                                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                                        <th scope="col" class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">Actions</th>
                                    </tr>
                                </thead>
                                <tbody class="bg-white divide-y divide-gray-200" id="productTableBody">
                                    <!-- Products will be loaded here -->
                                </tbody>
                            </table>
                        </div>
                        <div class="px-6 py-4 border-t border-gray-200 flex items-center justify-between">
                            <div class="text-sm text-gray-500">
                                Showing <span id="productStart">1</span> to <span id="productEnd">10</span> of <span id="productTotal">0</span> products
                            </div>
                            <div class="flex space-x-2">
                                <button id="productPrevPage" class="px-3 py-1 border border-gray-300 rounded-md text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 disabled:opacity-50">
                                    Previous
                                </button>
                                <button id="productNextPage" class="px-3 py-1 border border-gray-300 rounded-md text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 disabled:opacity-50">
                                    Next
                                </button>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Add/Edit Product Modal -->
                <div id="productModal" class="hidden fixed inset-0 bg-gray-600 bg-opacity-50 flex items-center justify-center z-50">
                    <div class="bg-white rounded-lg shadow-xl w-full max-w-3xl max-h-screen overflow-y-auto">
                        <div class="px-6 py-4 border-b border-gray-200">
                            <h3 class="text-lg font-medium text-gray-900" id="modalTitle">Add New Product</h3>
                        </div>
                        <div class="px-6 py-4">
                            <form id="productForm">
                                <input type="hidden" id="productId">
                                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                                    <div>
                                        <label for="productName" class="block text-sm font-medium text-gray-700 mb-1">Product Name</label>
                                        <input type="text" id="productName" name="productName" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                    </div>
                                    <div>
                                        <label for="productCategory" class="block text-sm font-medium text-gray-700 mb-1">Category</label>
                                        <select id="productCategory" name="productCategory" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                            <option value="">Select Category</option>
                                            <option value="protein">Protein</option>
                                            <option value="preworkout">Pre-Workout</option>
                                            <option value="vitamins">Vitamins</option>
                                            <option value="accessories">Accessories</option>
                                        </select>
                                    </div>
                                    <div>
                                        <label for="productPrice" class="block text-sm font-medium text-gray-700 mb-1">Price (AED)</label>
                                        <input type="number" id="productPrice" name="productPrice" step="0.01" min="0" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                    </div>
                                    <div>
                                        <label for="productOldPrice" class="block text-sm font-medium text-gray-700 mb-1">Old Price (AED)</label>
                                        <input type="number" id="productOldPrice" name="productOldPrice" step="0.01" min="0" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500">
                                    </div>
                                    <div>
                                        <label for="productStock" class="block text-sm font-medium text-gray-700 mb-1">Stock Quantity</label>
                                        <input type="number" id="productStock" name="productStock" min="0" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                    </div>
                                    <div>
                                        <label for="productStatus" class="block text-sm font-medium text-gray-700 mb-1">Status</label>
                                        <select id="productStatus" name="productStatus" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                            <option value="active">Active</option>
                                            <option value="inactive">Inactive</option>
                                            <option value="outofstock">Out of Stock</option>
                                        </select>
                                    </div>
                                    <div class="md:col-span-2">
                                        <label for="productDescription" class="block text-sm font-medium text-gray-700 mb-1">Description</label>
                                        <textarea id="productDescription" name="productDescription" rows="3" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"></textarea>
                                    </div>
                                    <div class="md:col-span-2">
                                        <label class="block text-sm font-medium text-gray-700 mb-1">Product Image</label>
                                        <div class="mt-1 flex items-center">
                                            <span class="inline-block h-24 w-24 rounded-md overflow-hidden bg-gray-100">
                                                <img id="productImagePreview" src="https://via.placeholder.com/150" alt="Product preview" class="h-full w-full object-cover">
                                            </span>
                                            <div class="ml-4">
                                                <label for="productImage" class="cursor-pointer bg-white py-2 px-3 border border-gray-300 rounded-md shadow-sm text-sm leading-4 font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                                                    <span>Upload Image</span>
                                                    <input id="productImage" name="productImage" type="file" class="sr-only" accept="image/*">
                                                </label>
                                                <p class="text-xs text-gray-500 mt-1">PNG, JPG, GIF up to 5MB</p>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </form>
                        </div>
                        <div class="px-6 py-4 border-t border-gray-200 flex justify-end space-x-3">
                            <button type="button" id="cancelProductBtn" class="px-4 py-2 border border-gray-300 rounded-md shadow-sm text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                                Cancel
                            </button>
                            <button type="button" id="saveProductBtn" class="px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                                Save Product
                            </button>
                        </div>
                    </div>
                </div>

                <!-- Settings View -->
                <div id="settingsView" class="hidden">
                    <div class="mb-6">
                        <h2 class="text-2xl font-semibold text-gray-800">Store Settings</h2>
                        <p class="text-sm text-gray-500">Configure your store preferences and contact information</p>
                    </div>

                    <div class="bg-white shadow rounded-lg overflow-hidden">
                        <div class="px-6 py-4 border-b border-gray-200">
                            <h3 class="text-lg font-medium text-gray-900">Contact Information</h3>
                        </div>
                        <div class="px-6 py-4">
                            <form id="contactSettingsForm">
                                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                                    <div>
                                        <label for="whatsappNumber" class="block text-sm font-medium text-gray-700 mb-1">WhatsApp Number</label>
                                        <input type="text" id="whatsappNumber" name="whatsappNumber" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                    </div>
                                    <div>
                                        <label for="callNumber" class="block text-sm font-medium text-gray-700 mb-1">Call Number</label>
                                        <input type="text" id="callNumber" name="callNumber" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                    </div>
                                    <div>
                                        <label for="storeEmail" class="block text-sm font-medium text-gray-700 mb-1">Email</label>
                                        <input type="email" id="storeEmail" name="storeEmail" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                    </div>
                                    <div>
                                        <label for="storeAddress" class="block text-sm font-medium text-gray-700 mb-1">Address</label>
                                        <input type="text" id="storeAddress" name="storeAddress" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                    </div>
                                </div>
                            </form>
                        </div>
                    </div>

                    <div class="bg-white shadow rounded-lg overflow-hidden mt-6">
                        <div class="px-6 py-4 border-b border-gray-200">
                            <h3 class="text-lg font-medium text-gray-900">Regional Settings</h3>
                        </div>
                        <div class="px-6 py-4">
                            <form id="regionalSettingsForm">
                                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                                    <div>
                                        <label for="storeRegion" class="block text-sm font-medium text-gray-700 mb-1">Default Region</label>
                                        <select id="storeRegion" name="storeRegion" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                            <option value="uae">United Arab Emirates (AED)</option>
                                            <option value="saudi">Saudi Arabia (SAR)</option>
                                            <option value="qar">Qatar (QAR)</option>
                                            <option value="kwd">Kuwait (KWD)</option>
                                            <option value="bhd">Bahrain (BHD)</option>
                                            <option value="irr">Iran (IRR)</option>
                                            <option value="iqd">Iraq (IQD)</option>
                                            <option value="usa">USA (USD)</option>
                                            <option value="inr">India (INR)</option>
                                            <option value="bdt">Bangladesh (BDT)</option>
                                        </select>
                                    </div>
                                    <div>
                                        <label for="storeLanguage" class="block text-sm font-medium text-gray-700 mb-1">Default Language</label>
                                        <select id="storeLanguage" name="storeLanguage" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                            <option value="english">English</option>
                                            <option value="arabic">Arabic</option>
                                            <option value="bengali">Bengali</option>
                                            <option value="hindi">Hindi</option>
                                        </select>
                                    </div>
                                    <div class="md:col-span-2">
                                        <label class="block text-sm font-medium text-gray-700 mb-1">Auto Price Conversion</label>
                                        <div class="flex items-center">
                                            <div class="relative inline-block w-10 mr-2 align-middle select-none">
                                                <input type="checkbox" id="autoPriceToggle" class="toggle-checkbox absolute block w-6 h-6 rounded-full bg-white border-4 appearance-none cursor-pointer" checked>
                                                <label for="autoPriceToggle" class="toggle-label block overflow-hidden h-6 rounded-full bg-gray-300 cursor-pointer"></label>
                                            </div>
                                            <label for="autoPriceToggle" class="text-sm text-gray-700">Enable automatic price conversion based on customer region</label>
                                        </div>
                                    </div>
                                </div>
                            </form>
                        </div>
                    </div>

                    <div class="bg-white shadow rounded-lg overflow-hidden mt-6">
                        <div class="px-6 py-4 border-b border-gray-200">
                            <h3 class="text-lg font-medium text-gray-900">Payment Methods</h3>
                        </div>
                        <div class="px-6 py-4">
                            <form id="paymentSettingsForm">
                                <div class="space-y-4">
                                    <div class="flex items-center">
                                        <input id="codPayment" name="paymentMethods" type="checkbox" class="h-4 w-4 text-indigo-600 focus:ring-indigo-500 border-gray-300 rounded" checked>
                                        <label for="codPayment" class="ml-2 block text-sm text-gray-700">Cash on Delivery (COD)</label>
                                    </div>
                                    <div class="flex items-center">
                                        <input id="stripePayment" name="paymentMethods" type="checkbox" class="h-4 w-4 text-indigo-600 focus:ring-indigo-500 border-gray-300 rounded">
                                        <label for="stripePayment" class="ml-2 block text-sm text-gray-700">Stripe</label>
                                    </div>
                                    <div class="flex items-center">
                                        <input id="paypalPayment" name="paymentMethods" type="checkbox" class="h-4 w-4 text-indigo-600 focus:ring-indigo-500 border-gray-300 rounded">
                                        <label for="paypalPayment" class="ml-2 block text-sm text-gray-700">PayPal</label>
                                    </div>
                                </div>
                            </form>
                        </div>
                    </div>

                    <div class="mt-6 flex justify-end">
                        <button id="saveSettingsBtn" class="px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                            Save Settings
                        </button>
                    </div>
                </div>
            </main>
        </div>
    </div>

    <!-- Notification Toast -->
    <div id="notificationToast" class="hidden fixed bottom-4 right-4 bg-green-500 text-white px-4 py-2 rounded-md shadow-lg">
        <div class="flex items-center">
            <i class="fas fa-check-circle mr-2"></i>
            <span id="notificationMessage">Settings saved successfully!</span>
        </div>
    </div>

    <script>
        // Firebase Configuration
        const firebaseConfig = {
            apiKey: "YOUR_API_KEY",
            authDomain: "dabaoprotein.firebaseapp.com",
            projectId: "dabaoprotein",
            storageBucket: "dabaoprotein.appspot.com",
            messagingSenderId: "YOUR_SENDER_ID",
            appId: "YOUR_APP_ID"
        };

        // Initialize Firebase
        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.firestore();
        const storage = firebase.storage();

        // API Configuration
        const API_BASE_URL = 'https://your-backend-api.com'; // Replace with your backend URL
        
        // Global state
        let currentUser = null;
        let products = [];
        let settings = {
            contact: {
                whatsapp: '+971508496533',
                call: '0554162919',
                email: 'adnanqwer1211@gmail.com',
                address: 'Dubai, UAE'
            },
            regional: {
                defaultRegion: 'uae',
                defaultLanguage: 'english',
                autoPriceConversion: true,
                currencyRates: {
                    aed: 1.0,
                    sar: 1.02,
                    qar: 1.01,
                    kwd: 0.082,
                    bhd: 0.10,
                    irr: 11250.0,
                    iqd: 1310.0,
                    usd: 0.27,
                    inr: 22.0,
                    bdt: 30.0
                }
            },
            paymentMethods: {
                cod: true,
                stripe: false,
                paypal: false
            }
        };

        // DOM Elements
        const loginScreen = document.getElementById('loginScreen');
        const adminPanel = document.getElementById('adminPanel');
        const loginForm = document.getElementById('loginForm');
        const loginBtn = document.getElementById('loginBtn');
        const logoutBtn = document.getElementById('logoutBtn');
        
        // Navigation Links
        const dashboardLink = document.getElementById('dashboardLink');
        const productsLink = document.getElementById('productsLink');
        const settingsLink = document.getElementById('settingsLink');
        
        // Views
        const dashboardView = document.getElementById('dashboardView');
        const productsView = document.getElementById('productsView');
        const settingsView = document.getElementById('settingsView');
        
        // Dashboard Elements
        const totalProducts = document.getElementById('totalProducts');
        const activeProducts = document.getElementById('activeProducts');
        const outOfStockProducts = document.getElementById('outOfStockProducts');
        const totalCategories = document.getElementById('totalCategories');
        const recentProductsBody = document.getElementById('recentProductsBody');
        
        // Product Management
        const addProductBtn = document.getElementById('addProductBtn');
        const productModal = document.getElementById('productModal');
        const productForm = document.getElementById('productForm');
        const productId = document.getElementById('productId');
        const productName = document.getElementById('productName');
        const productCategory = document.getElementById('productCategory');
        const productPrice = document.getElementById('productPrice');
        const productOldPrice = document.getElementById('productOldPrice');
        const productStock = document.getElementById('productStock');
        const productStatus = document.getElementById('productStatus');
        const productDescription = document.getElementById('productDescription');
        const productImage = document.getElementById('productImage');
        const productImagePreview = document.getElementById('productImagePreview');
        const saveProductBtn = document.getElementById('saveProductBtn');
        const cancelProductBtn = document.getElementById('cancelProductBtn');
        const productTableBody = document.getElementById('productTableBody');
        const productCategoryFilter = document.getElementById('productCategoryFilter');
        const productSearch = document.getElementById('productSearch');
        
        // Settings
        const contactSettingsForm = document.getElementById('contactSettingsForm');
        const regionalSettingsForm = document.getElementById('regionalSettingsForm');
        const paymentSettingsForm = document.getElementById('paymentSettingsForm');
        const whatsappNumber = document.getElementById('whatsappNumber');
        const callNumber = document.getElementById('callNumber');
        const storeEmail = document.getElementById('storeEmail');
        const storeAddress = document.getElementById('storeAddress');
        const storeRegion = document.getElementById('storeRegion');
        const storeLanguage = document.getElementById('storeLanguage');
        const autoPriceToggle = document.getElementById('autoPriceToggle');
        const codPayment = document.getElementById('codPayment');
        const stripePayment = document.getElementById('stripePayment');
        const paypalPayment = document.getElementById('paypalPayment');
        const saveSettingsBtn = document.getElementById('saveSettingsBtn');
        
        // Notifications
        const notificationToast = document.getElementById('notificationToast');
        const notificationMessage = document.getElementById('notificationMessage');
        
        // Page Title
        const pageTitle = document.getElementById('pageTitle');
        const adminName = document.getElementById('adminName');
        const adminAvatar = document.getElementById('adminAvatar');

        // Current state
        let currentView = 'dashboard';
        let editingProductId = null;

        // API Functions
        async function apiRequest(url, method = 'GET', data = null, requiresAuth = true) {
            const headers = {
                'Content-Type': 'application/json'
            };
            
            if (requiresAuth && currentUser) {
                const token = await currentUser.getIdToken();
                headers['Authorization'] = `Bearer ${token}`;
            }
            
            const config = {
                method,
                headers
            };
            
            if (data) {
                config.body = JSON.stringify(data);
            }
            
            try {
                const response = await fetch(`${API_BASE_URL}${url}`, config);
                
                if (!response.ok) {
                    const errorData = await response.json();
                    throw new Error(errorData.message || 'API request failed');
                }
                
                return await response.json();
            } catch (error) {
                console.error('API request error:', error);
                throw error;
            }
        }

        // Authentication Functions
        async function login(email, password) {
            try {
                showLoading();
                const userCredential = await auth.signInWithEmailAndPassword(email, password);
                currentUser = userCredential.user;
                
                if (email !== "adnanqwer1211@gmail.com") {
                    await auth.signOut();
                    throw new Error("Unauthorized access");
                }
                
                // Initialize admin panel
                await initAdminPanel();
                
                // Hide login screen and show admin panel
                loginScreen.classList.add('hidden');
                adminPanel.classList.remove('hidden');
                
                // Update admin profile info
                updateAdminProfile();
                
                showNotification('Login successful!');
            } catch (error) {
                showNotification(error.message || 'Login failed', 'error');
            } finally {
                hideLoading();
            }
        }

        async function logout() {
            try {
                showLoading();
                await auth.signOut();
                currentUser = null;
                
                // Show login screen and hide admin panel
                loginScreen.classList.remove('hidden');
                adminPanel.classList.add('hidden');
                loginForm.reset();
            } catch (error) {
                console.error('Logout error:', error);
            } finally {
                hideLoading();
            }
        }

        async function checkAuth() {
            auth.onAuthStateChanged(async (user) => {
                if (user && user.email === "adnanqwer1211@gmail.com") {
                    currentUser = user;
                    
                    // Initialize admin panel
                    await initAdminPanel();
                    
                    // Hide login screen and show admin panel
                    loginScreen.classList.add('hidden');
                    adminPanel.classList.remove('hidden');
                    
                    // Update admin profile info
                    updateAdminProfile();
                } else {
                    loginScreen.classList.remove('hidden');
                }
            });
        }

        // Data Fetching Functions
        async function fetchProducts() {
            try {
                showLoading();
                const snapshot = await db.collection('products').orderBy('createdAt', 'desc').get();
                products = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                renderProducts();
                updateDashboardStats();
            } catch (error) {
                showNotification(error.message || 'Failed to load products', 'error');
            } finally {
                hideLoading();
            }
        }

        async function fetchSettings() {
            try {
                showLoading();
                const doc = await db.collection('settings').doc('store_settings').get();
                if (doc.exists) {
                    settings = doc.data();
                    renderSettings();
                }
            } catch (error) {
                console.error('Failed to load settings:', error);
            } finally {
                hideLoading();
            }
        }

        function updateDashboardStats() {
            totalProducts.textContent = products.length;
            activeProducts.textContent = products.filter(p => p.status === 'active').length;
            outOfStockProducts.textContent = products.filter(p => p.status === 'outofstock').length;
            renderRecentProducts(products.slice(0, 5));
        }

        // Data Saving Functions
        async function saveProductToAPI(productData) {
            try {
                showLoading();
                
                if (editingProductId) {
                    // Update existing product
                    await db.collection('products').doc(editingProductId).update({
                        ...productData,
                        updatedAt: firebase.firestore.FieldValue.serverTimestamp()
                    });
                    showNotification('Product updated successfully!');
                } else {
                    // Add new product
                    await db.collection('products').add({
                        ...productData,
                        createdAt: firebase.firestore.FieldValue.serverTimestamp(),
                        updatedAt: firebase.firestore.FieldValue.serverTimestamp()
                    });
                    showNotification('Product added successfully!');
                }
                
                // Refresh products list
                await fetchProducts();
            } catch (error) {
                showNotification(error.message || 'Failed to save product', 'error');
                throw error;
            } finally {
                hideLoading();
            }
        }

        async function deleteProductFromAPI(productId) {
            try {
                showLoading();
                await db.collection('products').doc(productId).delete();
                showNotification('Product deleted successfully!');
                await fetchProducts();
            } catch (error) {
                showNotification(error.message || 'Failed to delete product', 'error');
            } finally {
                hideLoading();
            }
        }

        async function saveSettingsToAPI() {
            try {
                showLoading();
                
                // Prepare settings data from form
                const settingsData = {
                    contact: {
                        whatsapp: whatsappNumber.value,
                        call: callNumber.value,
                        email: storeEmail.value,
                        address: storeAddress.value
                    },
                    regional: {
                        defaultRegion: storeRegion.value,
                        defaultLanguage: storeLanguage.value,
                        autoPriceConversion: autoPriceToggle.checked,
                        currencyRates: settings.regional.currencyRates
                    },
                    paymentMethods: {
                        cod: codPayment.checked,
                        stripe: stripePayment.checked,
                        paypal: paypalPayment.checked
                    },
                    updatedAt: firebase.firestore.FieldValue.serverTimestamp()
                };
                
                await db.collection('settings').doc('store_settings').set(settingsData, { merge: true });
                settings = settingsData;
                showNotification('Settings saved successfully!');
            } catch (error) {
                showNotification(error.message || 'Failed to save settings', 'error');
                throw error;
            } finally {
                hideLoading();
            }
        }

        // Rendering Functions
        function renderProducts(filteredProducts = products) {
            productTableBody.innerHTML = '';
            
            filteredProducts.forEach(product => {
                const row = document.createElement('tr');
                
                // Status badge
                let statusBadge = '';
                if (product.status === 'active') {
                    statusBadge = '<span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-green-100 text-green-800">Active</span>';
                } else if (product.status === 'inactive') {
                    statusBadge = '<span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-gray-100 text-gray-800">Inactive</span>';
                } else if (product.status === 'outofstock') {
                    statusBadge = '<span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-red-100 text-red-800">Out of Stock</span>';
                }
                
                // Discount calculation
                let discount = '';
                if (product.oldPrice && product.oldPrice > 0 && product.oldPrice > product.price) {
                    const discountPercent = Math.round(((product.oldPrice - product.price) / product.oldPrice) * 100);
                    discount = `<span class="ml-2 text-xs text-red-500">${discountPercent}% OFF</span>`;
                }
                
                // Price display
                const priceDisplay = product.oldPrice && product.oldPrice > 0 
                    ? `<span class="text-gray-500 line-through mr-1">AED ${product.oldPrice.toFixed(2)}</span> AED ${product.price.toFixed(2)} ${discount}`
                    : `AED ${product.price.toFixed(2)}`;
                
                row.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap">
                        <img src="${product.image}" alt="${product.name}" class="h-10 w-10 rounded-md object-cover">
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap">
                        <div class="text-sm font-medium text-gray-900">${product.name}</div>
                        <div class="text-sm text-gray-500 truncate max-w-xs">${product.description}</div>
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 capitalize">${product.category}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${priceDisplay}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${product.stock}</td>
                    <td class="px-6 py-4 whitespace-nowrap">${statusBadge}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                        <button class="text-indigo-600 hover:text-indigo-900 mr-3 edit-product" data-id="${product.id}">Edit</button>
                        <button class="text-red-600 hover:text-red-900 delete-product" data-id="${product.id}">Delete</button>
                    </td>
                `;
                
                productTableBody.appendChild(row);
            });
            
            // Update product counts
            document.getElementById('productTotal').textContent = filteredProducts.length;
            
            // Add event listeners to edit and delete buttons
            document.querySelectorAll('.edit-product').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const productId = e.target.getAttribute('data-id');
                    editProduct(productId);
                });
            });
            
            document.querySelectorAll('.delete-product').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const productId = e.target.getAttribute('data-id');
                    deleteProduct(productId);
                });
            });
        }

        function renderRecentProducts(products) {
            recentProductsBody.innerHTML = '';
            
            products.forEach(product => {
                const row = document.createElement('tr');
                
                let statusBadge = '';
                if (product.status === 'active') {
                    statusBadge = '<span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-green-100 text-green-800">Active</span>';
                } else if (product.status === 'inactive') {
                    statusBadge = '<span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-gray-100 text-gray-800">Inactive</span>';
                } else if (product.status === 'outofstock') {
                    statusBadge = '<span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-red-100 text-red-800">Out of Stock</span>';
                }
                
                row.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap">
                        <img src="${product.image}" alt="${product.name}" class="h-10 w-10 rounded-md object-cover">
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap">
                        <div class="text-sm font-medium text-gray-900">${product.name}</div>
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">AED ${product.price.toFixed(2)}</td>
                    <td class="px-6 py-4 whitespace-nowrap">${statusBadge}</td>
                `;
                
                recentProductsBody.appendChild(row);
            });
        }

        function renderSettings() {
            // Contact settings
            whatsappNumber.value = settings.contact.whatsapp;
            callNumber.value = settings.contact.call;
            storeEmail.value = settings.contact.email;
            storeAddress.value = settings.contact.address;
            
            // Regional settings
            storeRegion.value = settings.regional.defaultRegion;
            storeLanguage.value = settings.regional.defaultLanguage;
            autoPriceToggle.checked = settings.regional.autoPriceConversion;
            
            // Payment methods
            codPayment.checked = settings.paymentMethods.cod;
            stripePayment.checked = settings.paymentMethods.stripe;
            paypalPayment.checked = settings.paymentMethods.paypal;
        }

        function updateAdminProfile() {
            if (currentUser) {
                adminName.textContent = currentUser.email;
            }
        }

        // Product Management Functions
        function openProductModal() {
            document.getElementById('modalTitle').textContent = 'Add New Product';
            productForm.reset();
            productImagePreview.src = 'https://via.placeholder.com/150';
            editingProductId = null;
            productModal.classList.remove('hidden');
        }

        function editProduct(id) {
            const product = products.find(p => p.id === id);
            
            if (product) {
                document.getElementById('modalTitle').textContent = 'Edit Product';
                productId.value = product.id;
                productName.value = product.name;
                productCategory.value = product.category;
                productPrice.value = product.price;
                productOldPrice.value = product.oldPrice || '';
                productStock.value = product.stock;
                productStatus.value = product.status;
                productDescription.value = product.description;
                productImagePreview.src = product.image;
                editingProductId = product.id;
                productModal.classList.remove('hidden');
            }
        }

        function handleProductImageUpload(e) {
            const file = e.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (event) => {
                    productImagePreview.src = event.target.result;
                };
                reader.readAsDataURL(file);
            }
        }

        async function saveProduct() {
            // Validate form
            if (!productForm.checkValidity()) {
                productForm.reportValidity();
                return;
            }
            
            const productData = {
                name: productName.value,
                category: productCategory.value,
                price: parseFloat(productPrice.value),
                oldPrice: productOldPrice.value ? parseFloat(productOldPrice.value) : 0,
                stock: parseInt(productStock.value),
                status: productStatus.value,
                description: productDescription.value,
                image: productImagePreview.src
            };
            
            try {
                await saveProductToAPI(productData);
                closeProductModal();
            } catch (error) {
                // Error is already handled in saveProductToAPI
            }
        }

        async function deleteProduct(id) {
            if (confirm('Are you sure you want to delete this product?')) {
                await deleteProductFromAPI(id);
            }
        }

        function closeProductModal() {
            productModal.classList.add('hidden');
        }

        // Settings Functions
        async function saveSettings() {
            try {
                await saveSettingsToAPI();
            } catch (error) {
                // Error is already handled in saveSettingsToAPI
            }
        }

        // Filter products by category
        function filterProducts() {
            const category = productCategoryFilter.value;
            
            if (category === 'all') {
                renderProducts(products);
            } else {
                const filtered = products.filter(p => p.category === category);
                renderProducts(filtered);
            }
        }

        // Search products
        function searchProducts() {
            const searchTerm = productSearch.value.toLowerCase();
            
            if (searchTerm === '') {
                filterProducts();
                return;
            }
            
            const filtered = products.filter(p => 
                p.name.toLowerCase().includes(searchTerm) || 
                p.description.toLowerCase().includes(searchTerm) ||
                p.category.toLowerCase().includes(searchTerm)
            );
            
            renderProducts(filtered);
        }

        // Navigation Functions
        function switchView(view) {
            // Hide all views
            dashboardView.classList.add('hidden');
            productsView.classList.add('hidden');
            settingsView.classList.add('hidden');
            
            // Remove active class from all nav links
            document.querySelectorAll('.sidebar-link').forEach(link => {
                link.classList.remove('bg-indigo-600', 'text-white');
                link.classList.add('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
            });
            
            // Show selected view and update active nav link
            currentView = view;
            switch(view) {
                case 'dashboard':
                    dashboardView.classList.remove('hidden');
                    dashboardLink.classList.add('bg-indigo-600', 'text-white');
                    dashboardLink.classList.remove('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
                    pageTitle.textContent = 'Dashboard';
                    updateDashboardStats();
                    break;
                case 'products':
                    productsView.classList.remove('hidden');
                    productsLink.classList.add('bg-indigo-600', 'text-white');
                    productsLink.classList.remove('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
                    pageTitle.textContent = 'Product Management';
                    fetchProducts();
                    break;
                case 'settings':
                    settingsView.classList.remove('hidden');
                    settingsLink.classList.add('bg-indigo-600', 'text-white');
                    settingsLink.classList.remove('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
                    pageTitle.textContent = 'Store Settings';
                    fetchSettings();
                    break;
            }
        }

        // Notification Functions
        function showNotification(message, type = 'success') {
            notificationMessage.textContent = message;
            
            // Set background color based on type
            if (type === 'success') {
                notificationToast.classList.remove('bg-red-500');
                notificationToast.classList.add('bg-green-500');
            } else {
                notificationToast.classList.remove('bg-green-500');
                notificationToast.classList.add('bg-red-500');
            }
            
            notificationToast.classList.remove('hidden');
            
            setTimeout(() => {
                notificationToast.classList.add('hidden');
            }, 3000);
        }

        // Loading Functions
        function showLoading() {
            loginBtn.innerHTML = '<div class="spinner"></div>';
            loginBtn.disabled = true;
        }

        function hideLoading() {
            loginBtn.innerHTML = 'Sign In';
            loginBtn.disabled = false;
        }

        // Initialization
        async function initAdminPanel() {
            await fetchProducts();
            await fetchSettings();
            switchView('dashboard');
        }

        // Event Listeners
        loginForm.addEventListener('submit', async (e) => {
            e.preventDefault();
            const email = document.getElementById('email').value;
            const password = document.getElementById('password').value;
            await login(email, password);
        });

        logoutBtn.addEventListener('click', logout);
        
        // Navigation
        dashboardLink.addEventListener('click', (e) => {
            e.preventDefault();
            switchView('dashboard');
        });
        
        productsLink.addEventListener('click', (e) => {
            e.preventDefault();
            switchView('products');
        });
        
        settingsLink.addEventListener('click', (e) => {
            e.preventDefault();
            switchView('settings');
        });
        
        // Product Management
        addProductBtn.addEventListener('click', openProductModal);
        addProductQuickBtn.addEventListener('click', openProductModal);
        manageSettingsQuickBtn.addEventListener('click', () => switchView('settings'));
        viewProductsQuickBtn.addEventListener('click', () => switchView('products'));
        refreshDataQuickBtn.addEventListener('click', () => {
            if (currentView === 'dashboard') {
                fetchProducts();
            } else if (currentView === 'products') {
                fetchProducts();
            } else if (currentView === 'settings') {
                fetchSettings();
            }
        });
        
        productImage.addEventListener('change', handleProductImageUpload);
        saveProductBtn.addEventListener('click', saveProduct);
        cancelProductBtn.addEventListener('click', closeProductModal);
        
        productCategoryFilter.addEventListener('change', filterProducts);
        productSearch.addEventListener('input', searchProducts);
        
        // Settings
        saveSettingsBtn.addEventListener('click', saveSettings);
        
        // Initialize
        document.addEventListener('DOMContentLoaded', () => {
            checkAuth();
        });
    </script>
</body>
</html>
