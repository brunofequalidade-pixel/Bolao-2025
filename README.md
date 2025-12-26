<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bolão Mega da Virada 2025</title>

    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">

    <style>
        .sena-bg { background-color: #dcfce7; border: 2px solid #16a34a; }
        .quina-bg { background-color: #fef9c3; border: 2px solid #ca8a04; }
        .quadra-bg { background-color: #dbeafe; border: 2px solid #2563eb; }
        .hit-number { background-color: #16a34a; color: white; font-weight: bold; border-color: #16a34a; }
    </style>
</head>

<body class="bg-gray-100 min-h-screen pb-20">

<!-- NAV -->
<nav class="bg-green-700 text-white p-4 shadow-lg sticky top-0 z-50">
    <div class="container mx-auto flex flex-col md:flex-row justify-between items-center gap-4">
        <h1 class="text-2xl font-bold flex items-center gap-2">
            <i class="fas fa-clover"></i> Bolão Mega da Virada
        </h1>

        <input type="text" id="searchInput"
               placeholder="Pesquisar por nome ou dezenas (ex: 05 12 33)"
               class="w-full md:w-1/2 p-2 rounded text-gray-800 focus:outline-none">

        <button onclick="toggleAdmin()"
                class="bg-green-800 px-3 py-1 rounded text-xs border border-green-600">
            Admin
        </button>
    </div>
</nav>

<!-- RESUMO -->
<div class="container mx-auto mt-6 px-4">
    <div class="bg-white p-4 rounded-lg shadow-md grid grid-cols-3 gap-2 text-center font-bold">
        <div class="bg-green-50 p-2 rounded border border-green-500 text-green-700">
            <div class="text-[10px] uppercase">Sena</div>
            <span id="countSena">0</span>
        </div>
        <div class="bg-yellow-50 p-2 rounded border border-yellow-500 text-yellow-700">
            <div class="text-[10px] uppercase">Quina</div>
            <span id="countQuina">0</span>
        </div>
        <div class="bg-blue-50 p-2 rounded border border-blue-500 text-blue-700">
            <div class="text-[10px] uppercase">Quadra</div>
            <span id="countQuadra">0</span>
        </div>
    </div>
</div>

<!-- ADMIN -->
<div id="adminPanel" class="hidden container mx-auto mt-6 px-4">
    <div class="bg-gray-200 p-4 rounded-lg border border-gray-300 shadow-inner">

        <div class="mb-4 bg-white p-3 rounded shadow-sm">
            <label class="block font-bold mb-1 text-sm text-gray-700">Resultado do Sorteio:</label>
            <div class="flex gap-2">
                <input type="text" id="drawInput"
                       placeholder="Ex: 01 02 03 04 05 06"
                       class="flex-1 p-2 rounded border border-gray-300">
                <button onclick="saveDrawResult()"
                        class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">
                    Salvar
                </button>
            </div>
        </div>

        <div class="bg-white p-3 rounded shadow-sm">
            <label class="block font-bold mb-1 text-sm text-gray-700">Importar Excel:</label>
            <input type="file" id="fileInput" accept=".xlsx"
                   class="block w-full text-sm text-gray-500
                   file:mr-4 file:py-2 file:px-4
                   file:rounded-full file:border-0
                   file:bg-green-50 file:text-green-700
                   hover:file:bg-green-100"/>
        </div>
    </div>
</div>

<!-- LISTA -->
<div class="container mx-auto mt-6 px-4">
    <div id="loading" class="text-center py-10 text-gray-500">
        <i class="fas fa-spinner fa-spin fa-2x"></i>
        <p class="mt-2">Carregando bolão...</p>
    </div>
    <div id="betsList" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4"></div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import {
    getFirestore, collection, getDocs, doc,
    setDoc, onSnapshot, writeBatch
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

const firebaseConfig = {
    apiKey: "AIzaSyBo8G3ZcWk4EepN0cHdVBtXc7tGOfcw-yg",
    authDomain: "inscricaosinuca.firebaseapp.com",
    projectId: "inscricaosinuca",
    storageBucket: "i
