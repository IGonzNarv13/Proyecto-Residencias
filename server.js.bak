import express from "express";
import multer from "multer";
import fs from "fs";
import path from "path";
import { fileURLToPath } from "url";

const app = express();

// Configuración de multer para aceptar solo PDFs
const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        cb(null, "uploads/");
    },
    filename: (req, file, cb) => {
        cb(null, Date.now() + "-" + file.originalname); // evita sobrescribir
    }
});

function fileFilter(req, file, cb) {
    if (file.mimetype === "application/pdf") {
        cb(null, true);
    } else {
        cb(new Error("Solo se permiten archivos PDF"), false);
    }
}

const upload = multer({ storage, fileFilter });

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

// Servir estáticos
app.use(express.static(path.join(__dirname, "public")));
app.use("/uploads", express.static(path.join(__dirname, "uploads"))); 

// Página principal
app.get("/", (req, res) => {
    res.sendFile(path.join(__dirname, "public", "index.html"));
});

// Nueva ruta que devuelve lista de archivos en JSON
app.get("/archivos", (req, res) => {
    const files = fs.readdirSync(path.join(__dirname, "uploads"));
    const lista = files.map(f => ({
        nombre: f,
        url: "/uploads/" + f
    }));
    res.json(lista);
});

// Subida de archivos
app.post("/upload", upload.single("archivo"), (req, res) => {
    res.redirect("/");
});

// Manejo de errores de multer
app.use((err, req, res, next) => {
    if (err.message === "Solo se permiten archivos PDF") {
        return res.status(400).send("<h2>Error: Solo se permiten archivos PDF</h2><a href='/'>Volver</a>");
    }
    next(err);
});

app.listen(3000, () => {
    console.log("Servidor corriendo en http://localhost:3000");
});
