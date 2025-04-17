<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DabaoProtein Admin Panel</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        /* Custom scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f1f1;
        }
        ::-webkit-scrollbar-thumb {
            background: #888;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #555;
        }
        
        /* Animation for notifications */
        @keyframes slideIn {
            from { transform: translateX(100%); }
            to { transform: translateX(0); }
        }
        
        .notification {
            animation: slideIn 0.3s ease-out;
        }
        
        /* Custom toggle switch */
        .toggle-checkbox:checked {
            right: 0;
            border-color: #10B981;
        }
        .toggle-checkbox:checked + .toggle-label {
            background-color: #10B981;
        }
        
        /* Loading spinner */
        .spinner {
            width: 24px;
            height: 24px;
            border: 3px solid rgba(255,255,255,.3);
            border-radius: 50%;
            border-top-color: #fff;
            animation: spin 1s ease-in-out infinite;
        }
        
        @keyframes spin {
            to { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="bg-gray-100 font-sans">
    <!-- Loading Screen -->
    <div id="loadingScreen" class="fixed inset-0 flex items-center justify-center bg-gray-900 bg-opacity-90 z-50">
        <div class="spinner"></div>
    </div>

    <!-- Login Screen -->
    <div id="loginScreen" class="fixed inset-0 flex items-center justify-center bg-gray-900 bg-opacity-90 z-40">
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

    <!-- Main Admin Panel -->
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
                <a href="#" id="dashboardLink" class="flex items-center px-4 py-3 text-sm font-medium text-white bg-gray-700 rounded-lg mx-2">
                    <i class="fas fa-tachometer-alt mr-3"></i>
                    Dashboard
                </a>
                <a href="#" id="productsLink" class="flex items-center px-4 py-3 text-sm font-medium text-gray-300 hover:text-white hover:bg-gray-700 rounded-lg mx-2">
                    <i class="fas fa-boxes mr-3"></i>
                    Products
                </a>
                <a href="#" id="settingsLink" class="flex items-center px-4 py-3 text-sm font-medium text-gray-300 hover:text-white hover:bg-gray-700 rounded-lg mx-2">
                    <i class="fas fa-cog mr-3"></i>
                    Settings
                </a>
                <a href="#" id="bannersLink" class="flex items-center px-4 py-3 text-sm font-medium text-gray-300 hover:text-white hover:bg-gray-700 rounded-lg mx-2">
                    <i class="fas fa-image mr-3"></i>
                    Banners
                </a>
                <div class="px-4 py-2 text-xs font-semibold text-gray-400 uppercase tracking-wider mt-4">
                    Reports
                </div>
                <a href="#" id="analyticsLink" class="flex items-center px-4 py-3 text-sm font-medium text-gray-300 hover:text-white hover:bg-gray-700 rounded-lg mx-2">
                    <i class="fas fa-chart-line mr-3"></i>
                    Analytics
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
                            <button id="notificationBtn" class="p-1 rounded-full text-gray-500 hover:text-gray-700 focus:outline-none">
                                <i class="fas fa-bell"></i>
                                <span id="notificationBadge" class="hidden absolute top-0 right-0 h-2 w-2 rounded-full bg-red-500"></span>
                            </button>
                            <div id="notificationDropdown" class="hidden absolute right-0 mt-2 w-72 bg-white rounded-md shadow-lg py-1 z-50">
                                <div class="px-4 py-3 border-b">
                                    <p class="text-sm font-medium text-gray-800">Notifications</p>
                                </div>
                                <div id="notificationList" class="max-h-60 overflow-y-auto">
                                    <!-- Notifications will be loaded here -->
                                </div>
                                <div class="px-4 py-2 text-center border-t">
                                    <a href="#" class="text-sm font-medium text-indigo-600 hover:text-indigo-500">View all</a>
                                </div>
                            </div>
                        </div>
                        <div class="relative">
                            <button id="profileBtn" class="flex items-center space-x-2 focus:outline-none">
                                <img id="adminAvatar" class="h-8 w-8 rounded-full" src="https://via.placeholder.com/32" alt="Admin profile">
                                <span id="adminName" class="text-sm font-medium text-gray-700">Admin</span>
                            </button>
                            <div id="profileDropdown" class="hidden absolute right-0 mt-2 w-48 bg-white rounded-md shadow-lg py-1 z-50">
                                <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">Your Profile</a>
                                <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">Settings</a>
                                <div class="border-t border-gray-200"></div>
                                <button id="logoutDropdownBtn" class="block w-full text-left px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">Sign out</button>
                            </div>
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
                                    <p class="text-sm font-medium text-gray-500">Orders Today</p>
                                    <p class="text-2xl font-semibold text-gray-800" id="ordersToday">0</p>
                                </div>
                                <div class="p-3 rounded-full bg-green-100 text-green-600">
                                    <i class="fas fa-shopping-cart text-xl"></i>
                                </div>
                            </div>
                        </div>
                        <div class="bg-white p-6 rounded-lg shadow">
                            <div class="flex items-center justify-between">
                                <div>
                                    <p class="text-sm font-medium text-gray-500">Revenue</p>
                                    <p class="text-2xl font-semibold text-gray-800" id="totalRevenue">AED 0</p>
                                </div>
                                <div class="p-3 rounded-full bg-blue-100 text-blue-600">
                                    <i class="fas fa-money-bill-wave text-xl"></i>
                                </div>
                            </div>
                        </div>
                        <div class="bg-white p-6 rounded-lg shadow">
                            <div class="flex items-center justify-between">
                                <div>
                                    <p class="text-sm font-medium text-gray-500">Low Stock</p>
                                    <p class="text-2xl font-semibold text-gray-800" id="lowStockItems">0</p>
                                </div>
                                <div class="p-3 rounded-full bg-yellow-100 text-yellow-600">
                                    <i class="fas fa-exclamation-triangle text-xl"></i>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                        <div class="bg-white p-6 rounded-lg shadow">
                            <div class="flex items-center justify-between mb-4">
                                <h2 class="text-lg font-semibold text-gray-800">Recent Orders</h2>
                                <a href="#" class="text-sm text-indigo-600 hover:text-indigo-500">View All</a>
                            </div>
                            <div class="overflow-x-auto">
                                <table class="min-w-full divide-y divide-gray-200">
                                    <thead class="bg-gray-50">
                                        <tr>
                                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Order ID</th>
                                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Customer</th>
                                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Amount</th>
                                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                                        </tr>
                                    </thead>
                                    <tbody id="recentOrdersBody" class="bg-white divide-y divide-gray-200">
                                        <!-- Orders will be loaded here -->
                                    </tbody>
                                </table>
                            </div>
                        </div>
                        <div class="bg-white p-6 rounded-lg shadow">
                            <div class="flex items-center justify-between mb-4">
                                <h2 class="text-lg font-semibold text-gray-800">Top Selling Products</h2>
                                <a href="#" class="text-sm text-indigo-600 hover:text-indigo-500">View All</a>
                            </div>
                            <div id="topProductsList" class="space-y-4">
                                <!-- Top products will be loaded here -->
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
                                        <input type="text" id="whatsappNumber" name="whatsappNumber" value="+971508496533" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                    </div>
                                    <div>
                                        <label for="callNumber" class="block text-sm font-medium text-gray-700 mb-1">Call Number</label>
                                        <input type="text" id="callNumber" name="callNumber" value="0554162919" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
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
                                            <option value="uae" selected>United Arab Emirates (AED)</option>
                                            <option value="saudi">Saudi Arabia (SAR)</option>
                                            <option value="qatar">Qatar (QAR)</option>
                                            <option value="kuwait">Kuwait (KWD)</option>
                                            <option value="bahrain">Bahrain (BHD)</option>
                                            <option value="iran">Iran (IRR)</option>
                                            <option value="iraq">Iraq (IQD)</option>
                                            <option value="usa">USA (USD)</option>
                                            <option value="india">India (INR)</option>
                                            <option value="bangladesh">Bangladesh (BDT)</option>
                                        </select>
                                    </div>
                                    <div>
                                        <label for="storeLanguage" class="block text-sm font-medium text-gray-700 mb-1">Default Language</label>
                                        <select id="storeLanguage" name="storeLanguage" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" required>
                                            <option value="english" selected>English</option>
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

                <!-- Banners View -->
                <div id="bannersView" class="hidden">
                    <div class="mb-6">
                        <h2 class="text-2xl font-semibold text-gray-800">Banner Management</h2>
                        <p class="text-sm text-gray-500">Manage your homepage banners and promotional offers</p>
                    </div>

                    <div class="bg-white shadow rounded-lg overflow-hidden">
                        <div class="px-6 py-4 border-b border-gray-200 flex justify-between items-center">
                            <h3 class="text-lg font-medium text-gray-900">Current Banners</h3>
                            <button id="addBannerBtn" class="px-4 py-2 bg-indigo-600 text-white rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2">
                                <i class="fas fa-plus mr-2"></i> Add Banner
                            </button>
                        </div>
                        <div class="px-6 py-4">
                            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6" id="bannersGrid">
                                <!-- Banners will be loaded here -->
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Add/Edit Banner Modal -->
                <div id="bannerModal" class="hidden fixed inset-0 bg-gray-600 bg-opacity-50 flex items-center justify-center z-50">
                    <div class="bg-white rounded-lg shadow-xl w-full max-w-2xl max-h-screen overflow-y-auto">
                        <div class="px-6 py-4 border-b border-gray-200">
                            <h3 class="text-lg font-medium text-gray-900" id="bannerModalTitle">Add New Banner</h3>
                        </div>
                        <div class="px-6 py-4">
                            <form id="bannerForm">
                                <input type="hidden" id="bannerId">
                                <div class="space-y-6">
                                    <div>
                                        <label for="bannerTitle" class="block text-sm font-medium text-gray-700 mb-1">Banner Title</label>
                                        <input type="text" id="bannerTitle" name="bannerTitle" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500">
                                    </div>
                                    <div>
                                        <label for="bannerLink" class="block text-sm font-medium text-gray-700 mb-1">Link URL</label>
                                        <input type="url" id="bannerLink" name="bannerLink" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" placeholder="https://example.com">
                                    </div>
                                    <div>
                                        <label class="block text-sm font-medium text-gray-700 mb-1">Banner Image</label>
                                        <div class="mt-1 flex justify-center px-6 pt-5 pb-6 border-2 border-gray-300 border-dashed rounded-md">
                                            <div class="space-y-1 text-center">
                                                <div class="flex text-sm text-gray-600">
                                                    <label for="bannerImage" class="relative cursor-pointer bg-white rounded-md font-medium text-indigo-600 hover:text-indigo-500 focus-within:outline-none focus-within:ring-2 focus-within:ring-offset-2 focus-within:ring-indigo-500">
                                                        <span>Upload a file</span>
                                                        <input id="bannerImage" name="bannerImage" type="file" class="sr-only" accept="image/*">
                                                    </label>
                                                    <p class="pl-1">or drag and drop</p>
                                                </div>
                                                <p class="text-xs text-gray-500">PNG, JPG, GIF up to 10MB</p>
                                            </div>
                                        </div>
                                        <div class="mt-4">
                                            <img id="bannerImagePreview" src="https://via.placeholder.com/800x300" alt="Banner preview" class="w-full h-auto rounded-md">
                                        </div>
                                    </div>
                                    <div>
                                        <label for="bannerStatus" class="block text-sm font-medium text-gray-700 mb-1">Status</label>
                                        <select id="bannerStatus" name="bannerStatus" class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500">
                                            <option value="active">Active</option>
                                            <option value="inactive">Inactive</option>
                                        </select>
                                    </div>
                                </div>
                            </form>
                        </div>
                        <div class="px-6 py-4 border-t border-gray-200 flex justify-end space-x-3">
                            <button type="button" id="cancelBannerBtn" class="px-4 py-2 border border-gray-300 rounded-md shadow-sm text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                                Cancel
                            </button>
                            <button type="button" id="saveBannerBtn" class="px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                                Save Banner
                            </button>
                        </div>
                    </div>
                </div>

                <!-- Analytics View -->
                <div id="analyticsView" class="hidden">
                    <div class="mb-6">
                        <h2 class="text-2xl font-semibold text-gray-800">Store Analytics</h2>
                        <p class="text-sm text-gray-500">Track your store performance and customer behavior</p>
                    </div>

                    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                        <div class="bg-white p-6 rounded-lg shadow">
                            <h3 class="text-lg font-medium text-gray-900 mb-4">Sales Overview</h3>
                            <div class="h-64 flex items-center justify-center">
                                <p class="text-gray-500">Sales chart will be displayed here</p>
                            </div>
                        </div>
                        <div class="bg-white p-6 rounded-lg shadow">
                            <h3 class="text-lg font-medium text-gray-900 mb-4">Top Products</h3>
                            <div class="h-64 flex items-center justify-center">
                                <p class="text-gray-500">Product performance chart will be displayed here</p>
                            </div>
                        </div>
                    </div>

                    <div class="bg-white shadow rounded-lg overflow-hidden">
                        <div class="px-6 py-4 border-b border-gray-200">
                            <h3 class="text-lg font-medium text-gray-900">Traffic Sources</h3>
                        </div>
                        <div class="px-6 py-4">
                            <div class="h-64 flex items-center justify-center">
                                <p class="text-gray-500">Traffic sources chart will be displayed here</p>
                            </div>
                        </div>
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
        // API Configuration
        const API_BASE_URL = 'https://dabaoprotein-api.example.com'; // Replace with your actual API URL
        const ADMIN_EMAIL = 'adnanqwer1211@gmail.com';
        
        // Global state
        let authToken = null;
        let currentUser = null;
        let products = [];
        let banners = [];
        let settings = {
            contact: {
                whatsapp: '+971508496533',
                call: '0554162919'
            },
            regional: {
                defaultRegion: 'uae',
                defaultLanguage: 'english',
                autoPriceConversion: true
            },
            paymentMethods: {
                cod: true,
                stripe: false,
                paypal: false
            }
        };

        // DOM Elements
        const loadingScreen = document.getElementById('loadingScreen');
        const loginScreen = document.getElementById('loginScreen');
        const adminPanel = document.getElementById('adminPanel');
        const loginForm = document.getElementById('loginForm');
        const loginBtn = document.getElementById('loginBtn');
        const logoutBtn = document.getElementById('logoutBtn');
        const logoutDropdownBtn = document.getElementById('logoutDropdownBtn');
        
        // Navigation Links
        const dashboardLink = document.getElementById('dashboardLink');
        const productsLink = document.getElementById('productsLink');
        const settingsLink = document.getElementById('settingsLink');
        const bannersLink = document.getElementById('bannersLink');
        const analyticsLink = document.getElementById('analyticsLink');
        
        // Views
        const dashboardView = document.getElementById('dashboardView');
        const productsView = document.getElementById('productsView');
        const settingsView = document.getElementById('settingsView');
        const bannersView = document.getElementById('bannersView');
        const analyticsView = document.getElementById('analyticsView');
        
        // Dashboard Elements
        const totalProducts = document.getElementById('totalProducts');
        const ordersToday = document.getElementById('ordersToday');
        const totalRevenue = document.getElementById('totalRevenue');
        const lowStockItems = document.getElementById('lowStockItems');
        const recentOrdersBody = document.getElementById('recentOrdersBody');
        const topProductsList = document.getElementById('topProductsList');
        
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
        
        // Banner Management
        const addBannerBtn = document.getElementById('addBannerBtn');
        const bannerModal = document.getElementById('bannerModal');
        const bannerForm = document.getElementById('bannerForm');
        const bannerId = document.getElementById('bannerId');
        const bannerTitle = document.getElementById('bannerTitle');
        const bannerLink = document.getElementById('bannerLink');
        const bannerImage = document.getElementById('bannerImage');
        const bannerImagePreview = document.getElementById('bannerImagePreview');
        const bannerStatus = document.getElementById('bannerStatus');
        const saveBannerBtn = document.getElementById('saveBannerBtn');
        const cancelBannerBtn = document.getElementById('cancelBannerBtn');
        const bannersGrid = document.getElementById('bannersGrid');
        
        // Settings
        const contactSettingsForm = document.getElementById('contactSettingsForm');
        const regionalSettingsForm = document.getElementById('regionalSettingsForm');
        const paymentSettingsForm = document.getElementById('paymentSettingsForm');
        const whatsappNumber = document.getElementById('whatsappNumber');
        const callNumber = document.getElementById('callNumber');
        const storeRegion = document.getElementById('storeRegion');
        const storeLanguage = document.getElementById('storeLanguage');
        const autoPriceToggle = document.getElementById('autoPriceToggle');
        const codPayment = document.getElementById('codPayment');
        const stripePayment = document.getElementById('stripePayment');
        const paypalPayment = document.getElementById('paypalPayment');
        const saveSettingsBtn = document.getElementById('saveSettingsBtn');
        
        // Notifications
        const notificationBtn = document.getElementById('notificationBtn');
        const notificationDropdown = document.getElementById('notificationDropdown');
        const notificationList = document.getElementById('notificationList');
        const notificationBadge = document.getElementById('notificationBadge');
        const profileBtn = document.getElementById('profileBtn');
        const profileDropdown = document.getElementById('profileDropdown');
        const adminAvatar = document.getElementById('adminAvatar');
        const adminName = document.getElementById('adminName');
        const notificationToast = document.getElementById('notificationToast');
        const notificationMessage = document.getElementById('notificationMessage');
        
        // Page Title
        const pageTitle = document.getElementById('pageTitle');

        // Current state
        let currentView = 'dashboard';
        let editingProductId = null;
        let editingBannerId = null;

        // API Functions
        async function apiRequest(url, method = 'GET', data = null, requiresAuth = true) {
            const headers = {
                'Content-Type': 'application/json'
            };
            
            if (requiresAuth && authToken) {
                headers['Authorization'] = `Bearer ${authToken}`;
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
                const response = await apiRequest('/auth/login', 'POST', { email, password }, false);
                
                authToken = response.token;
                currentUser = response.user;
                
                // Store token in localStorage
                localStorage.setItem('dabaoprotein_admin_token', authToken);
                
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
                await apiRequest('/auth/logout', 'POST');
            } catch (error) {
                console.error('Logout error:', error);
            } finally {
                // Clear auth token and user data
                authToken = null;
                currentUser = null;
                localStorage.removeItem('dabaoprotein_admin_token');
                
                // Show login screen and hide admin panel
                loginScreen.classList.remove('hidden');
                adminPanel.classList.add('hidden');
                loginForm.reset();
                
                hideLoading();
            }
        }

        async function checkAuth() {
            const token = localStorage.getItem('dabaoprotein_admin_token');
            if (token) {
                try {
                    showLoading();
                    authToken = token;
                    
                    // Verify token
                    const response = await apiRequest('/auth/verify');
                    currentUser = response.user;
                    
                    // Initialize admin panel
                    await initAdminPanel();
                    
                    // Hide login screen and show admin panel
                    loginScreen.classList.add('hidden');
                    adminPanel.classList.remove('hidden');
                    
                    // Update admin profile info
                    updateAdminProfile();
                } catch (error) {
                    console.error('Auth verification failed:', error);
                    localStorage.removeItem('dabaoprotein_admin_token');
                    authToken = null;
                    currentUser = null;
                    loginScreen.classList.remove('hidden');
                } finally {
                    hideLoading();
                }
            }
        }

        // Data Fetching Functions
        async function fetchProducts() {
            try {
                showLoading();
                const data = await apiRequest('/products');
                products = data;
                renderProducts();
            } catch (error) {
                showNotification(error.message || 'Failed to load products', 'error');
            } finally {
                hideLoading();
            }
        }

        async function fetchBanners() {
            try {
                showLoading();
                const data = await apiRequest('/banners');
                banners = data;
                renderBanners();
            } catch (error) {
                showNotification(error.message || 'Failed to load banners', 'error');
            } finally {
                hideLoading();
            }
        }

        async function fetchSettings() {
            try {
                showLoading();
                const data = await apiRequest('/settings');
                settings = data;
                renderSettings();
            } catch (error) {
                console.error('Failed to load settings:', error);
            } finally {
                hideLoading();
            }
        }

        async function fetchDashboardData() {
            try {
                showLoading();
                const data = await apiRequest('/dashboard');
                
                // Update dashboard metrics
                totalProducts.textContent = data.totalProducts;
                ordersToday.textContent = data.ordersToday;
                totalRevenue.textContent = `AED ${data.totalRevenue.toFixed(2)}`;
                lowStockItems.textContent = data.lowStockItems;
                
                // Render recent orders
                renderRecentOrders(data.recentOrders);
                
                // Render top products
                renderTopProducts(data.topProducts);
            } catch (error) {
                console.error('Failed to load dashboard data:', error);
            } finally {
                hideLoading();
            }
        }

        // Data Saving Functions
        async function saveProductToAPI(productData) {
            try {
                showLoading();
                
                if (editingProductId) {
                    // Update existing product
                    await apiRequest(`/products/${editingProductId}`, 'PUT', productData);
                    showNotification('Product updated successfully!');
                } else {
                    // Add new product
                    await apiRequest('/products', 'POST', productData);
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
                await apiRequest(`/products/${productId}`, 'DELETE');
                showNotification('Product deleted successfully!');
                await fetchProducts();
            } catch (error) {
                showNotification(error.message || 'Failed to delete product', 'error');
            } finally {
                hideLoading();
            }
        }

        async function saveBannerToAPI(bannerData) {
            try {
                showLoading();
                
                if (editingBannerId) {
                    // Update existing banner
                    await apiRequest(`/banners/${editingBannerId}`, 'PUT', bannerData);
                    showNotification('Banner updated successfully!');
                } else {
                    // Add new banner
                    await apiRequest('/banners', 'POST', bannerData);
                    showNotification('Banner added successfully!');
                }
                
                // Refresh banners list
                await fetchBanners();
            } catch (error) {
                showNotification(error.message || 'Failed to save banner', 'error');
                throw error;
            } finally {
                hideLoading();
            }
        }

        async function deleteBannerFromAPI(bannerId) {
            try {
                showLoading();
                await apiRequest(`/banners/${bannerId}`, 'DELETE');
                showNotification('Banner deleted successfully!');
                await fetchBanners();
            } catch (error) {
                showNotification(error.message || 'Failed to delete banner', 'error');
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
                        call: callNumber.value
                    },
                    regional: {
                        defaultRegion: storeRegion.value,
                        defaultLanguage: storeLanguage.value,
                        autoPriceConversion: autoPriceToggle.checked
                    },
                    paymentMethods: {
                        cod: codPayment.checked,
                        stripe: stripePayment.checked,
                        paypal: paypalPayment.checked
                    }
                };
                
                await apiRequest('/settings', 'PUT', settingsData);
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
                } else if (product.status === 'lowstock') {
                    statusBadge = '<span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-yellow-100 text-yellow-800">Low Stock</span>';
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

        function renderBanners() {
            bannersGrid.innerHTML = '';
            
            banners.forEach(banner => {
                const bannerCard = document.createElement('div');
                bannerCard.className = 'bg-white rounded-lg overflow-hidden shadow';
                
                bannerCard.innerHTML = `
                    <div class="relative">
                        <img src="${banner.image}" alt="${banner.title}" class="w-full h-40 object-cover">
                        <span class="absolute top-2 right-2 px-2 py-1 text-xs font-medium rounded-full ${banner.status === 'active' ? 'bg-green-100 text-green-800' : 'bg-gray-100 text-gray-800'}">
                            ${banner.status === 'active' ? 'Active' : 'Inactive'}
                        </span>
                    </div>
                    <div class="p-4">
                        <h3 class="text-lg font-medium text-gray-900 mb-1">${banner.title}</h3>
                        <p class="text-sm text-gray-500 mb-2">Link: ${banner.link}</p>
                        <div class="flex justify-between">
                            <button class="text-indigo-600 hover:text-indigo-900 text-sm font-medium edit-banner" data-id="${banner.id}">
                                <i class="fas fa-edit mr-1"></i> Edit
                            </button>
                            <button class="text-red-600 hover:text-red-900 text-sm font-medium delete-banner" data-id="${banner.id}">
                                <i class="fas fa-trash-alt mr-1"></i> Delete
                            </button>
                        </div>
                    </div>
                `;
                
                bannersGrid.appendChild(bannerCard);
            });
            
            // Add event listeners to edit and delete buttons
            document.querySelectorAll('.edit-banner').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const bannerId = e.target.closest('button').getAttribute('data-id');
                    editBanner(bannerId);
                });
            });
            
            document.querySelectorAll('.delete-banner').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const bannerId = e.target.closest('button').getAttribute('data-id');
                    deleteBanner(bannerId);
                });
            });
        }

        function renderSettings() {
            // Contact settings
            whatsappNumber.value = settings.contact.whatsapp;
            callNumber.value = settings.contact.call;
            
            // Regional settings
            storeRegion.value = settings.regional.defaultRegion;
            storeLanguage.value = settings.regional.defaultLanguage;
            autoPriceToggle.checked = settings.regional.autoPriceConversion;
            
            // Payment methods
            codPayment.checked = settings.paymentMethods.cod;
            stripePayment.checked = settings.paymentMethods.stripe;
            paypalPayment.checked = settings.paymentMethods.paypal;
        }

        function renderRecentOrders(orders) {
            recentOrdersBody.innerHTML = '';
            
            orders.forEach(order => {
                const row = document.createElement('tr');
                
                let statusBadge = '';
                if (order.status === 'completed') {
                    statusBadge = '<span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-green-100 text-green-800">Completed</span>';
                } else if (order.status === 'pending') {
                    statusBadge = '<span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-yellow-100 text-yellow-800">Pending</span>';
                } else if (order.status === 'shipped') {
                    statusBadge = '<span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-blue-100 text-blue-800">Shipped</span>';
                } else if (order.status === 'cancelled') {
                    statusBadge = '<span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-red-100 text-red-800">Cancelled</span>';
                }
                
                row.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">#${order.id}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${order.customer}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">AED ${order.amount.toFixed(2)}</td>
                    <td class="px-6 py-4 whitespace-nowrap">${statusBadge}</td>
                `;
                
                recentOrdersBody.appendChild(row);
            });
        }

        function renderTopProducts(products) {
            topProductsList.innerHTML = '';
            
            products.forEach((product, index) => {
                const bgColors = ['bg-indigo-100', 'bg-green-100', 'bg-blue-100'];
                const textColors = ['text-indigo-600', 'text-green-600', 'text-blue-600'];
                const bgColor = bgColors[index % bgColors.length];
                const textColor = textColors[index % textColors.length];
                
                const productDiv = document.createElement('div');
                productDiv.className = 'flex items-center';
                
                productDiv.innerHTML = `
                    <div class="flex-shrink-0 h-10 w-10 rounded-md ${bgColor} flex items-center justify-center">
                        <i class="fas fa-box ${textColor}"></i>
                    </div>
                    <div class="ml-4">
                        <p class="text-sm font-medium text-gray-900">${product.name}</p>
                        <p class="text-sm text-gray-500">AED ${product.price.toFixed(2)} | Sold: ${product.sold}</p>
                    </div>
                `;
                
                topProductsList.appendChild(productDiv);
            });
        }

        function updateAdminProfile() {
            if (currentUser) {
                adminName.textContent = currentUser.name;
                adminAvatar.src = currentUser.avatar || 'https://via.placeholder.com/32';
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
            const product = products.find(p => p.id === parseInt(id));
            
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
            
            // Determine stock status based on quantity
            if (productData.stock <= 0) {
                productData.status = 'outofstock';
            } else if (productData.stock <= 5) {
                productData.status = 'lowstock';
            } else {
                productData.status = productStatus.value;
            }
            
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

        // Banner Management Functions
        function openBannerModal() {
            document.getElementById('bannerModalTitle').textContent = 'Add New Banner';
            bannerForm.reset();
            bannerImagePreview.src = 'https://via.placeholder.com/800x300';
            editingBannerId = null;
            bannerModal.classList.remove('hidden');
        }
        
        function editBanner(id) {
            const banner = banners.find(b => b.id === parseInt(id));
            
            if (banner) {
                document.getElementById('bannerModalTitle').textContent = 'Edit Banner';
                bannerId.value = banner.id;
                bannerTitle.value = banner.title;
                bannerLink.value = banner.link;
                bannerStatus.value = banner.status;
                bannerImagePreview.src = banner.image;
                editingBannerId = banner.id;
                bannerModal.classList.remove('hidden');
            }
        }

        function handleBannerImageUpload(e) {
            const file = e.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (event) => {
                    bannerImagePreview.src = event.target.result;
                };
                reader.readAsDataURL(file);
            }
        }

        async function saveBanner() {
            // Validate form
            if (!bannerForm.checkValidity()) {
                bannerForm.reportValidity();
                return;
            }
            
            const bannerData = {
                title: bannerTitle.value,
                link: bannerLink.value,
                image: bannerImagePreview.src,
                status: bannerStatus.value
            };
            
            try {
                await saveBannerToAPI(bannerData);
                closeBannerModal();
            } catch (error) {
                // Error is already handled in saveBannerToAPI
            }
        }

        async function deleteBanner(id) {
            if (confirm('Are you sure you want to delete this banner?')) {
                await deleteBannerFromAPI(id);
            }
        }

        function closeBannerModal() {
            bannerModal.classList.add('hidden');
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
            bannersView.classList.add('hidden');
            analyticsView.classList.add('hidden');
            
            // Remove active class from all nav links
            dashboardLink.classList.remove('bg-gray-700', 'text-white');
            dashboardLink.classList.add('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
            productsLink.classList.remove('bg-gray-700', 'text-white');
            productsLink.classList.add('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
            settingsLink.classList.remove('bg-gray-700', 'text-white');
            settingsLink.classList.add('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
            bannersLink.classList.remove('bg-gray-700', 'text-white');
            bannersLink.classList.add('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
            analyticsLink.classList.remove('bg-gray-700', 'text-white');
            analyticsLink.classList.add('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
            
            // Show selected view and update active nav link
            currentView = view;
            switch(view) {
                case 'dashboard':
                    dashboardView.classList.remove('hidden');
                    dashboardLink.classList.add('bg-gray-700', 'text-white');
                    dashboardLink.classList.remove('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
                    pageTitle.textContent = 'Dashboard';
                    fetchDashboardData();
                    break;
                case 'products':
                    productsView.classList.remove('hidden');
                    productsLink.classList.add('bg-gray-700', 'text-white');
                    productsLink.classList.remove('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
                    pageTitle.textContent = 'Product Management';
                    fetchProducts();
                    break;
                case 'settings':
                    settingsView.classList.remove('hidden');
                    settingsLink.classList.add('bg-gray-700', 'text-white');
                    settingsLink.classList.remove('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
                    pageTitle.textContent = 'Store Settings';
                    fetchSettings();
                    break;
                case 'banners':
                    bannersView.classList.remove('hidden');
                    bannersLink.classList.add('bg-gray-700', 'text-white');
                    bannersLink.classList.remove('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
                    pageTitle.textContent = 'Banner Management';
                    fetchBanners();
                    break;
                case 'analytics':
                    analyticsView.classList.remove('hidden');
                    analyticsLink.classList.add('bg-gray-700', 'text-white');
                    analyticsLink.classList.remove('text-gray-300', 'hover:text-white', 'hover:bg-gray-700');
                    pageTitle.textContent = 'Store Analytics';
                    break;
            }
        }

        // Notification Functions
        function toggleNotificationDropdown() {
            notificationDropdown.classList.toggle('hidden');
            profileDropdown.classList.add('hidden');
            
            // Mark notifications as read when dropdown is opened
            if (!notificationDropdown.classList.contains('hidden')) {
                notificationBadge.classList.add('hidden');
            }
        }

        function toggleProfileDropdown() {
            profileDropdown.classList.toggle('hidden');
            notificationDropdown.classList.add('hidden');
        }

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
</html>
