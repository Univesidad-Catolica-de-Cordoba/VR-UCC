<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tablero de Comando - Vicerrectorado Académico UCC</title>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body {
            margin: 0;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen', 'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
        }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect } = React;

        // 🎯 CONFIGURACIÓN PRINCIPAL - CAMBIAR AQUÍ PARA ACTIVAR GOOGLE SHEETS REAL
        const MODO_PRODUCCION = true; // Cambiar a true para usar Google Sheets real
        const GOOGLE_SCRIPT_URL = MODO_PRODUCCION 
            ? 'https://script.google.com/macros/s/AKfycbxeV8FXqYRwC7QhlET-JCZUzzwXRiDmZjZp_uFYRFfMg-F-peYot80lAEEuLYlI_mwM/exec'
            : 'DEMO_MODE';

        // Iconos simples usando caracteres Unicode
        const Icons = {
            User: () => <span className="text-lg">👤</span>,
            Eye: () => <span className="text-lg">👁️</span>,
            Calendar: () => <span className="text-lg">📅</span>,
            TrendingUp: () => <span className="text-lg">📈</span>,
            FileText: () => <span className="text-lg">📄</span>,
            Lock: () => <span className="text-lg">🔒</span>,
            Users: () => <span className="text-lg">👥</span>,
            Database: () => <span className="text-lg">💾</span>,
            Wifi: () => <span className="text-lg">📡</span>
        };

        const Dashboard = () => {
            const [currentUser, setCurrentUser] = useState(null);
            const [selectedArea, setSelectedArea] = useState(null);
            const [password, setPassword] = useState('');
            const [showPasswordError, setShowPasswordError] = useState(false);
            const [showPasswordField, setShowPasswordField] = useState(false);
            const [loading, setLoading] = useState(false);
            const [connectionStatus, setConnectionStatus] = useState('disconnected');

            // Estado de datos
            const [objetivos, setObjetivos] = useState([]);
            const [usuarios, setUsuarios] = useState([]);

            const [formulario, setFormulario] = useState({
                nombre: '',
                estado: 'Iniciado',
                porcentaje: 0,
                observaciones: ''
            });

            const estados = ['Iniciado', 'En proceso', 'En revisión', 'Finalizado', 'Pausado'];

            // Datos de simulación para modo demo
            const simularGoogleSheets = {
                usuarios: [
                    { id: 'marianna', nombre: 'Marianna Galli', rol: 'vicerrectora', area: 'Vicerrectorado' },
                    { id: 'lucas', nombre: 'Lucas Blangino', rol: 'director', area: 'Secretaría Académica' },
                    { id: 'graciela', nombre: 'Graciela Ascar', rol: 'director', area: 'Secretaría de Aseguramiento de la Calidad Educativa' },
                    { id: 'milton', nombre: 'Milton Escobar', rol: 'director', area: 'Secretaría de Asuntos Internacionales' },
                    { id: 'silvia', nombre: 'Silvia Fontana', rol: 'director', area: 'Secretaría de Investigación' },
                    { id: 'rosana', nombre: 'Rosana Enrico', rol: 'director', area: 'Secretaría de Pedagogía Universitaria' },
                    { id: 'maria', nombre: 'María del Rosario Orozco', rol: 'director', area: 'Secretaría de Proyección y Responsabilidad Social Universitaria' },
                    { id: 'sandra', nombre: 'Sandra Martín', rol: 'director', area: 'Sistema de Bibliotecas' },
                    { id: 'gonzalo', nombre: 'Gonzalo Bengochea', rol: 'director', area: 'Sistema Institucional de Educación a Distancia' },
                    { id: 'carla', nombre: 'Carla Slek', rol: 'director', area: 'Editorial UCC' }
                ],
                objetivos: []
            };

            // Sistema de reintentos para conexión robusta
            const fetchWithRetry = async (url, options = {}, retries = 3) => {
                for (let i = 0; i < retries; i++) {
                    try {
                        const response = await fetch(url, {
                            ...options,
                            mode: 'cors',
                            cache: 'no-cache'
                        });
                        
                        if (!response.ok) {
                            throw new Error(`HTTP ${response.status}: ${response.statusText}`);
                        }
                        
                        return response;
                    } catch (error) {
                        console.log(`Intento ${i + 1} falló:`, error.message);
                        if (i === retries - 1) throw error;
                        
                        await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
                    }
                }
            };

            // Cargar datos
            const cargarDatos = async () => {
                setLoading(true);
                setConnectionStatus('connecting');
                
                try {
                    if (GOOGLE_SCRIPT_URL === 'DEMO_MODE') {
                        // Modo demo
                        console.log('🎭 Ejecutando en MODO DEMO');
                        await new Promise(resolve => setTimeout(resolve, 1000));
                        
                        setObjetivos(simularGoogleSheets.objetivos);
                        setUsuarios(simularGoogleSheets.usuarios);
                        setConnectionStatus('connected');
                        return;
                    }

                    // Modo real - Conectar a Google Sheets
                    console.log('🔗 Conectando a Google Sheets real:', GOOGLE_SCRIPT_URL);
                    
                    const usuariosResponse = await fetchWithRetry(`${GOOGLE_SCRIPT_URL}?action=obtenerUsuarios`);
                    const usuariosText = await usuariosResponse.text();
                    const usuariosData = JSON.parse(usuariosText);
                    
                    const objetivosResponse = await fetchWithRetry(`${GOOGLE_SCRIPT_URL}?action=obtenerObjetivos`);
                    const objetivosText = await objetivosResponse.text();
                    const objetivosData = JSON.parse(objetivosText);
                    
                    if ((objetivosData.success || objetivosData.éxito) && (usuariosData.success || usuariosData.éxito)) {
                        setObjetivos(objetivosData.objetivos || []);
                        setUsuarios(usuariosData.usuarios || []);
                        setConnectionStatus('connected');
                        console.log('✅ Conectado a Google Sheets real');
                    } else {
                        throw new Error('Error del servidor');
                    }

                } catch (error) {
                    console.error('❌ Error cargando datos:', error);
                    setConnectionStatus('error');
                    
                    setUsuarios(simularGoogleSheets.usuarios);
                    setObjetivos([]);
                } finally {
                    setLoading(false);
                }
            };

            // Crear objetivo
            const crearObjetivo = async (objetivo) => {
                try {
                    if (GOOGLE_SCRIPT_URL === 'DEMO_MODE') {
                        // Modo demo
                        const id = Date.now().toString();
                        const fechaHoy = new Date().toLocaleDateString('es-AR');
                        
                        const nuevoObjetivo = {
                            id: id,
                            responsable: objetivo.responsable,
                            area: objetivo.area,
                            nombre: objetivo.nombre,
                            estado: objetivo.estado,
                            porcentaje: objetivo.porcentaje,
                            observaciones: objetivo.observaciones,
                            fechaCreacion: fechaHoy,
                            fechaActualizacion: fechaHoy,
                            historial: [{ fecha: fechaHoy, cambio: 'Objetivo creado', usuario: objetivo.responsable }]
                        };
                        
                        await new Promise(resolve => setTimeout(resolve, 500));
                        simularGoogleSheets.objetivos.push(nuevoObjetivo);
                        await cargarDatos();
                        return { success: true };
                    }

                    // Modo real
                    const response = await fetchWithRetry(GOOGLE_SCRIPT_URL, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({ action: 'crearObjetivo', objetivo: objetivo })
                    });

                    const result = await response.json();
                    
                    if (result.success || result.éxito) {
                        await cargarDatos();
                        return { success: true };
                    } else {
                        throw new Error(result.error || 'Error desconocido');
                    }
                } catch (error) {
                    console.error('❌ Error creando objetivo:', error);
                    return { success: false, error: error.message };
                }
            };

            // Actualizar objetivo
            const actualizarObjetivo = async (id, campo, valor) => {
                try {
                    if (GOOGLE_SCRIPT_URL === 'DEMO_MODE') {
                        // Modo demo
                        const fechaHoy = new Date().toLocaleDateString('es-AR');
                        const objetivoIndex = simularGoogleSheets.objetivos.findIndex(obj => obj.id === id);
                        
                        if (objetivoIndex !== -1) {
                            simularGoogleSheets.objetivos[objetivoIndex][campo] = valor;
                            simularGoogleSheets.objetivos[objetivoIndex].fechaActualizacion = fechaHoy;
                            
                            const cambioTexto = campo === 'porcentaje' 
                                ? `Porcentaje actualizado a "${valor}%"`
                                : `${campo.charAt(0).toUpperCase() + campo.slice(1)} actualizado a "${valor}"`;
                                
                            simularGoogleSheets.objetivos[objetivoIndex].historial.push({
                                fecha: fechaHoy,
                                cambio: cambioTexto,
                                usuario: currentUser.nombre
                            });
                            
                            await new Promise(resolve => setTimeout(resolve, 300));
                            await cargarDatos();
                        }
                        return;
                    }

                    // Modo real
                    const updates = { [campo]: valor, usuario: currentUser.nombre };
                    const response = await fetchWithRetry(GOOGLE_SCRIPT_URL, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({ action: 'actualizarObjetivo', id: id, updates: updates })
                    });

                    const result = await response.json();
                    
                    if (result.success || result.éxito) {
                        await cargarDatos();
                    } else {
                        throw new Error(result.error || 'Error desconocido');
                    }
                } catch (error) {
                    console.error('❌ Error actualizando objetivo:', error);
                    alert('Error actualizando objetivo: ' + error.message);
                }
            };

            // Test de conexión
            const testConexion = async () => {
                setLoading(true);
                setConnectionStatus('connecting');
                
                try {
                    if (GOOGLE_SCRIPT_URL === 'DEMO_MODE') {
                        await new Promise(resolve => setTimeout(resolve, 1000));
                        setConnectionStatus('connected');
                        alert('🎭 Sistema funcionando en MODO DEMO!\n\n✅ Todas las funcionalidades activas\n• Crear objetivos\n• Actualizar estados\n• Dashboard de Marianna\n• Historial de cambios');
                        await cargarDatos();
                        return;
                    }

                    // Test real
                    const response = await fetch(GOOGLE_SCRIPT_URL + '?action=obtenerUsuarios', {
                        method: 'GET',
                        mode: 'cors',
                        cache: 'no-cache'
                    });
                    
                    if (!response.ok) {
                        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
                    }
                    
                    const data = await response.json();
                    
                    if (data.success || data.éxito) {
                        setConnectionStatus('connected');
                        alert(`✅ ¡Conexión exitosa a Google Sheets!\n\nUsuarios encontrados: ${data.usuarios?.length || 0}\n¡El sistema está funcionando correctamente!`);
                        await cargarDatos();
                    } else {
                        throw new Error(data.error || 'Error del servidor');
                    }
                } catch (error) {
                    setConnectionStatus('error');
                    console.error('❌ Test falló:', error);
                    alert(`❌ Error de conexión:\n\n${error.message}\n\nVerifica la configuración de Google Apps Script.`);
                } finally {
                    setLoading(false);
                }
            };

            // Funciones auxiliares
            const getEstadoColor = (estado) => {
                const colores = {
                    'Iniciado': 'bg-blue-100 text-blue-800',
                    'En proceso': 'bg-yellow-100 text-yellow-800',
                    'En revisión': 'bg-purple-100 text-purple-800',
                    'Finalizado': 'bg-green-100 text-green-800',
                    'Pausado': 'bg-red-100 text-red-800'
                };
                return colores[estado] || 'bg-gray-100 text-gray-800';
            };

            const getPorcentajeColor = (porcentaje) => {
                if (porcentaje >= 75) return 'text-green-600';
                if (porcentaje >= 50) return 'text-yellow-600';
                if (porcentaje >= 25) return 'text-orange-600';
                return 'text-red-600';
            };

            const handleLogin = (usuario) => {
                if (usuario.id === 'marianna') {
                    if (password === 'vicerrectorado2025') {
                        setCurrentUser(usuario);
                        setPassword('');
                        setShowPasswordError(false);
                    } else {
                        setShowPasswordError(true);
                    }
                } else {
                    setCurrentUser(usuario);
                }
            };

            const handleLogout = () => {
                setCurrentUser(null);
                setSelectedArea(null);
                setPassword('');
                setShowPasswordError(false);
                setShowPasswordField(false);
            };

            const handleSubmitObjetivo = async () => {
                if (!formulario.nombre.trim()) {
                    alert('Por favor ingrese el nombre del objetivo');
                    return;
                }
                
                setLoading(true);

                const nuevoObjetivo = {
                    responsable: currentUser.nombre,
                    area: currentUser.area,
                    nombre: formulario.nombre.trim(),
                    estado: formulario.estado,
                    porcentaje: parseInt(formulario.porcentaje) || 0,
                    observaciones: formulario.observaciones.trim()
                };

                const result = await crearObjetivo(nuevoObjetivo);
                
                if (result.success) {
                    setFormulario({ nombre: '', estado: 'Iniciado', porcentaje: 0, observaciones: '' });
                    
                    if (GOOGLE_SCRIPT_URL === 'DEMO_MODE') {
                        alert(`🎭 ¡Objetivo "${nuevoObjetivo.nombre}" guardado en modo demo!\n\n✅ Marianna puede verlo inmediatamente\n✅ Métricas se actualizan automáticamente`);
                    } else {
                        alert(`✅ Objetivo "${nuevoObjetivo.nombre}" guardado exitosamente en Google Sheets.`);
                    }
                } else {
                    alert('❌ Error al guardar el objetivo: ' + result.error);
                }
                
                setLoading(false);
            };

            // Indicador de conexión
            const ConnectionIndicator = () => {
                const getStatusColor = () => {
                    switch(connectionStatus) {
                        case 'connected': return GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? 'text-blue-600' : 'text-green-600';
                        case 'connecting': return 'text-yellow-600';
                        case 'error': return 'text-red-600';
                        default: return 'text-gray-600';
                    }
                };

                const getStatusText = () => {
                    switch(connectionStatus) {
                        case 'connected': return GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? 'Modo Demo' : 'Conectado a Google Sheets';
                        case 'connecting': return 'Conectando...';
                        case 'error': return 'Error de conexión';
                        default: return 'Desconectado';
                    }
                };

                return (
                    <div className={`flex items-center text-xs ${getStatusColor()}`}>
                        <Icons.Database />
                        <span className="ml-1">{getStatusText()}</span>
                        {connectionStatus === 'connecting' && <div className="ml-2 w-2 h-2 bg-yellow-600 rounded-full animate-pulse"></div>}
                    </div>
                );
            };

            // Cargar datos al inicializar
            useEffect(() => {
                cargarDatos();
            }, []);

            // Pantalla de login
            if (!currentUser) {
                return (
                    <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 flex items-center justify-center p-4">
                        <div className="bg-white rounded-lg shadow-xl p-8 w-full max-w-md">
                            <div className="text-center mb-8">
                                <div className="mx-auto w-16 h-16 bg-blue-600 rounded-full flex items-center justify-center mb-4">
                                    <Icons.Users />
                                </div>
                                <h1 className="text-2xl font-bold text-gray-900 mb-2">Tablero de Comando</h1>
                                <p className="text-gray-600 mb-4">Vicerrectorado Académico UCC</p>
                                <div className="flex justify-center items-center space-x-4">
                                    <ConnectionIndicator />
                                    {connectionStatus === 'error' && (
                                        <button
                                            onClick={testConexion}
                                            disabled={loading}
                                            className="text-xs bg-red-100 text-red-700 px-3 py-1 rounded-full hover:bg-red-200 transition-colors disabled:opacity-50"
                                        >
                                            {loading ? '🔄' : '🧪'} Test Conexión
                                        </button>
                                    )}
                                </div>
                            </div>

                            <div className="space-y-4">
                                <div>
                                    <label className="block text-sm font-medium text-gray-700 mb-2">
                                        Seleccionar Usuario
                                    </label>
                                    <select 
                                        className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                        onChange={(e) => {
                                            const usuario = usuarios.find(u => u.id === e.target.value);
                                            if (usuario) {
                                                if (usuario.id === 'marianna') {
                                                    setShowPasswordField(true);
                                                } else {
                                                    setShowPasswordField(false);
                                                    handleLogin(usuario);
                                                }
                                            } else {
                                                setShowPasswordField(false);
                                            }
                                        }}
                                        defaultValue=""
                                        disabled={loading}
                                    >
                                        <option value="">-- Seleccione su nombre --</option>
                                        {usuarios.map(usuario => (
                                            <option key={usuario.id} value={usuario.id}>
                                                {usuario.nombre}
                                            </option>
                                        ))}
                                    </select>
                                </div>

                                {showPasswordField && (
                                    <div>
                                        <label className="block text-sm font-medium text-gray-700 mb-2">
                                            <Icons.Lock /> Contraseña
                                        </label>
                                        <input
                                            type="password"
                                            value={password}
                                            onChange={(e) => setPassword(e.target.value)}
                                            className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                            placeholder="Ingrese su contraseña"
                                        />
                                        {showPasswordError && (
                                            <p className="text-red-500 text-sm mt-1">Contraseña incorrecta</p>
                                        )}
                                        <button
                                            type="button"
                                            onClick={() => handleLogin(usuarios.find(u => u.id === 'marianna'))}
                                            className="w-full mt-3 bg-blue-600 text-white py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors"
                                        >
                                            Ingresar
                                        </button>
                                    </div>
                                )}
                            </div>

                            {/* Información del modo actual */}
                            <div className={`mt-6 p-4 rounded-lg text-sm ${GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? 'bg-blue-50 border border-blue-200' : 'bg-green-50 border border-green-200'}`}>
                                <div className={`flex items-center ${GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? 'text-blue-800' : 'text-green-800'} mb-2`}>
                                    <span className="text-lg mr-2">{GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? '🎭' : '✅'}</span>
                                    <strong>{GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? 'Modo Demo Activo' : 'Google Sheets Conectado'}</strong>
                                </div>
                                <p className={GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? 'text-blue-700' : 'text-green-700'}>
                                    {GOOGLE_SCRIPT_URL === 'DEMO_MODE' 
                                        ? 'Sistema funcionando con todas las funcionalidades. Los datos persisten durante la sesión.'
                                        : 'Conectado a Google Sheets real. Todos los datos se guardan permanentemente.'
                                    }
                                </p>
                            </div>
                        </div>
                    </div>
                );
            }

            // Vista de la Vicerrectora
            if (currentUser.rol === 'vicerrectora') {
                const areas = [...new Set(objetivos.map(obj => obj.area))];
                
                return (
                    <div className="min-h-screen bg-gray-50">
                        <nav className="bg-white shadow-sm border-b">
                            <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                                <div className="flex justify-between items-center h-16">
                                    <div className="flex items-center">
                                        <h1 className="text-xl font-semibold text-gray-900">Tablero de Comando - Vicerrectorado Académico</h1>
                                    </div>
                                    <div className="flex items-center space-x-4">
                                        <ConnectionIndicator />
                                        <span className="text-sm text-gray-700">Bienvenida, {currentUser.nombre}</span>
                                        <button
                                            onClick={handleLogout}
                                            className="text-sm text-blue-600 hover:text-blue-800"
                                        >
                                            Cerrar Sesión
                                        </button>
                                    </div>
                                </div>
                            </div>
                        </nav>

                        <div className="max-w-7xl mx-auto py-6 px-4 sm:px-6 lg:px-8">
                            {!selectedArea ? (
                                <div>
                                    <div className="mb-8">
                                        <div className="flex justify-between items-center mb-4">
                                            <h2 className="text-2xl font-bold text-gray-900">Vista General del Vicerrectorado</h2>
                                            <div className="flex space-x-2">
                                                {connectionStatus === 'error' && (
                                                    <button
                                                        onClick={testConexion}
                                                        disabled={loading}
                                                        className="bg-red-600 text-white px-4 py-2 rounded-lg hover:bg-red-700 transition-colors disabled:opacity-50 flex items-center text-sm"
                                                    >
                                                        <Icons.Wifi />
                                                        <span className="ml-2">{loading ? 'Probando...' : 'Test Conexión'}</span>
                                                    </button>
                                                )}
                                                <button
                                                    onClick={cargarDatos}
                                                    disabled={loading}
                                                    className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition-colors disabled:opacity-50 flex items-center text-sm"
                                                >
                                                    <Icons.Database />
                                                    <span className="ml-2">{loading ? 'Actualizando...' : 'Actualizar Datos'}</span>
                                                </button>
                                            </div>
                                        </div>
                                        
                                        <div className="grid grid-cols-1 md:grid-cols-4 gap-4 mb-6">
                                            <div className="bg-white p-6 rounded-lg shadow">
                                                <div className="flex items-center">
                                                    <div className="p-2 bg-blue-100 rounded-lg">
                                                        <Icons.FileText />
                                                    </div>
                                                    <div className="ml-4">
                                                        <p className="text-sm font-medium text-gray-600">Total Objetivos</p>
                                                        <p className="text-2xl font-semibold text-gray-900">{objetivos.length}</p>
                                                    </div>
                                                </div>
                                            </div>
                                            <div className="bg-white p-6 rounded-lg shadow">
                                                <div className="flex items-center">
                                                    <div className="p-2 bg-green-100 rounded-lg">
                                                        <Icons.TrendingUp />
                                                    </div>
                                                    <div className="ml-4">
                                                        <p className="text-sm font-medium text-gray-600">Promedio Avance</p>
                                                        <p className="text-2xl font-semibold text-gray-900">
                                                            {objetivos.length > 0 ? Math.round(objetivos.reduce((acc, obj) => acc + (obj.porcentaje || 0), 0) / objetivos.length) : 0}%
                                                        </p>
                                                    </div>
                                                </div>
                                            </div>
                                            <div className="bg-white p-6 rounded-lg shadow">
                                                <div className="flex items-center">
                                                    <div className="p-2 bg-yellow-100 rounded-lg">
                                                        <Icons.Calendar />
                                                    </div>
                                                    <div className="ml-4">
                                                        <p className="text-sm font-medium text-gray-600">En Proceso</p>
                                                        <p className="text-2xl font-semibold text-gray-900">
                                                            {objetivos.filter(obj => obj.estado === 'En proceso').length}
                                                        </p>
                                                    </div>
                                                </div>
                                            </div>
                                            <div className="bg-white p-6 rounded-lg shadow">
                                                <div className="flex items-center">
                                                    <div className="p-2 bg-purple-100 rounded-lg">
                                                        <Icons.Users />
                                                    </div>
                                                    <div className="ml-4">
                                                        <p className="text-sm font-medium text-gray-600">Áreas Activas</p>
                                                        <p className="text-2xl font-semibold text-gray-900">{areas.length}</p>
                                                    </div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>

                                    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                                        {usuarios.filter(u => u.rol === 'director').map(usuario => {
                                            const objetivosArea = objetivos.filter(obj => obj.area === usuario.area);
                                            const promedioAvance = objetivosArea.length > 0 
                                                ? Math.round(objetivosArea.reduce((acc, obj) => acc + (obj.porcentaje || 0), 0) / objetivosArea.length)
                                                : 0;
                                            
                                            return (
                                                <div key={usuario.id} className="bg-white rounded-lg shadow hover:shadow-md transition-shadow cursor-pointer"
                                                     onClick={() => setSelectedArea(usuario.area)}>
                                                    <div className="p-6">
                                                        <div className="flex items-center justify-between mb-4">
                                                            <div className="flex items-center">
                                                                <div className="p-2 bg-blue-100 rounded-lg">
                                                                    <Icons.User />
                                                                </div>
                                                                <div className="ml-3">
                                                                    <h3 className="text-lg font-semibold text-gray-900">{usuario.nombre}</h3>
                                                                    <p className="text-sm text-gray-600">{usuario.area}</p>
                                                                </div>
                                                            </div>
                                                            <Icons.Eye />
                                                        </div>
                                                        
                                                        <div className="space-y-2">
                                                            <div className="flex justify-between text-sm">
                                                                <span className="text-gray-600">Objetivos:</span>
                                                                <span className="font-medium">{objetivosArea.length}</span>
                                                            </div>
                                                            <div className="flex justify-between text-sm">
                                                                <span className="text-gray-600">Avance promedio:</span>
                                                                <span className={`font-medium ${getPorcentajeColor(promedioAvance)}`}>
                                                                    {promedioAvance}%
                                                                </span>
                                                            </div>
                                                        </div>
                                                    </div>
                                                </div>
                                            );
                                        })}
                                    </div>
                                </div>
                            ) : (
                                <div>
                                    <div className="mb-6">
                                        <button
                                            onClick={() => setSelectedArea(null)}
                                            className="text-blue-600 hover:text-blue-800 mb-4"
                                        >
                                            ← Volver al dashboard
                                        </button>
                                        <h2 className="text-2xl font-bold text-gray-900">{selectedArea}</h2>
                                    </div>

                                    <div className="space-y-4">
                                        {objetivos.filter(obj => obj.area === selectedArea).map(objetivo => (
                                            <div key={objetivo.id} className="bg-white rounded-lg shadow p-6">
                                                <div className="flex justify-between items-start mb-4">
                                                    <div className="flex-1 pr-4">
                                                        <h3 className="text-lg font-semibold text-gray-900 mb-2">{objetivo.nombre}</h3>
                                                        <p className="text-sm text-gray-600 mb-2">Responsable: {objetivo.responsable}</p>
                                                    </div>
                                                    <div className="flex items-center space-x-3">
                                                        <span className={`px-3 py-1 rounded-full text-sm font-medium ${getEstadoColor(objetivo.estado)}`}>
                                                            {objetivo.estado}
                                                        </span>
                                                        <span className={`text-2xl font-bold ${getPorcentajeColor(objetivo.porcentaje || 0)}`}>
                                                            {objetivo.porcentaje || 0}%
                                                        </span>
                                                    </div>
                                                </div>

                                                <div className="mb-4">
                                                    <div className="w-full bg-gray-200 rounded-full h-2">
                                                        <div 
                                                            className="bg-blue-600 h-2 rounded-full transition-all duration-300"
                                                            style={{ width: `${objetivo.porcentaje || 0}%` }}
                                                        ></div>
                                                    </div>
                                                </div>

                                                <div className="mb-4">
                                                    <h4 className="text-sm font-medium text-gray-700 mb-2">Observaciones actuales:</h4>
                                                    <p className="text-sm text-gray-600 bg-gray-50 p-3 rounded">{objetivo.observaciones || 'Sin observaciones'}</p>
                                                </div>

                                                <div>
                                                    <h4 className="text-sm font-medium text-gray-700 mb-2">Historial de cambios:</h4>
                                                    <div className="space-y-2">
                                                        {(objetivo.historial || []).slice(-3).reverse().map((entrada, index) => (
                                                            <div key={index} className="text-xs text-gray-500 bg-gray-50 p-2 rounded">
                                                                <span className="font-medium">{entrada.fecha}</span> - {entrada.cambio} ({entrada.usuario})
                                                            </div>
                                                        ))}
                                                    </div>
                                                </div>
                                            </div>
                                        ))}
                                    </div>
                                </div>
                            )}
                        </div>
                    </div>
                );
            }

            // Vista de Director/a
            return (
                <div className="min-h-screen bg-gray-50">
                    <nav className="bg-white shadow-sm border-b">
                        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                            <div className="flex justify-between items-center h-16">
                                <div className="flex items-center">
                                    <h1 className="text-xl font-semibold text-gray-900">Gestión de Objetivos - {currentUser.area}</h1>
                                </div>
                                <div className="flex items-center space-x-4">
                                    <ConnectionIndicator />
                                    <span className="text-sm text-gray-700">{currentUser.nombre}</span>
                                    <button
                                        onClick={handleLogout}
                                        className="text-sm text-blue-600 hover:text-blue-800"
                                    >
                                        Cerrar Sesión
                                    </button>
                                </div>
                            </div>
                        </div>
                    </nav>

                    <div className="max-w-4xl mx-auto py-6 px-4 sm:px-6 lg:px-8">
                        <div className="bg-white rounded-lg shadow p-6 mb-6">
                            <div className="flex items-center justify-between mb-6">
                                <h2 className="text-xl font-semibold text-gray-900">Nuevo Objetivo</h2>
                                <div className="flex items-center text-sm text-gray-600">
                                    {GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? (
                                        <>
                                            <span className="mr-2">🎭</span>
                                            <span>Modo Demo</span>
                                        </>
                                    ) : (
                                        <>
                                            <Icons.Database />
                                            <span className="ml-1">Google Sheets</span>
                                        </>
                                    )}
                                </div>
                            </div>
                            
                            <div className="space-y-4">
                                <div>
                                    <label className="block text-sm font-medium text-gray-700 mb-2">
                                        Nombre del Objetivo
                                    </label>
                                    <input
                                        type="text"
                                        required
                                        value={formulario.nombre}
                                        onChange={(e) => setFormulario({...formulario, nombre: e.target.value})}
                                        className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                        placeholder="Ingrese el nombre del objetivo"
                                        disabled={loading}
                                    />
                                </div>

                                <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                                    <div>
                                        <label className="block text-sm font-medium text-gray-700 mb-2">
                                            Estado del Objetivo
                                        </label>
                                        <select
                                            value={formulario.estado}
                                            onChange={(e) => setFormulario({...formulario, estado: e.target.value})}
                                            className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                            disabled={loading}
                                        >
                                            {estados.map(estado => (
                                                <option key={estado} value={estado}>{estado}</option>
                                            ))}
                                        </select>
                                    </div>

                                    <div>
                                        <label className="block text-sm font-medium text-gray-700 mb-2">
                                            Porcentaje de Avance
                                        </label>
                                        <input
                                            type="number"
                                            min="0"
                                            max="100"
                                            value={formulario.porcentaje}
                                            onChange={(e) => setFormulario({...formulario, porcentaje: e.target.value})}
                                            className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                            placeholder="0-100"
                                            disabled={loading}
                                        />
                                    </div>
                                </div>

                                <div>
                                    <label className="block text-sm font-medium text-gray-700 mb-2">
                                        Observaciones
                                    </label>
                                    <textarea
                                        value={formulario.observaciones}
                                        onChange={(e) => setFormulario({...formulario, observaciones: e.target.value})}
                                        rows={3}
                                        className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                        placeholder="Ingrese observaciones adicionales"
                                        disabled={loading}
                                    />
                                </div>

                                <div className="flex justify-end">
                                    <button
                                        onClick={handleSubmitObjetivo}
                                        disabled={loading}
                                        className="bg-blue-600 text-white py-3 px-6 rounded-lg hover:bg-blue-700 transition-colors disabled:opacity-50 flex items-center"
                                    >
                                        {loading ? (
                                            <>
                                                <div className="w-4 h-4 border-2 border-white border-t-transparent rounded-full animate-spin mr-2"></div>
                                                Guardando...
                                            </>
                                        ) : (
                                            <>
                                                <Icons.Database />
                                                <span className="ml-2">Guardar Objetivo</span>
                                            </>
                                        )}
                                    </button>
                                </div>
                            </div>
                        </div>

                        {/* Lista de objetivos del área */}
                        <div className="bg-white rounded-lg shadow p-6">
                            <div className="flex items-center justify-between mb-6">
                                <h2 className="text-xl font-semibold text-gray-900">Mis Objetivos</h2>
                                <div className="text-xs text-gray-500 bg-blue-50 px-3 py-1 rounded-full">
                                    {GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? '🎭 Modo Demo' : '📊 Google Sheets'}
                                </div>
                            </div>
                            
                            <div className="space-y-4">
                                {objetivos.filter(obj => obj.area === currentUser.area).map(objetivo => (
                                    <div key={objetivo.id} className="border border-gray-200 rounded-lg p-4">
                                        <div className="flex justify-between items-start mb-3">
                                            <h3 className="text-lg font-medium text-gray-900">{objetivo.nombre}</h3>
                                            <span className={`px-3 py-1 rounded-full text-sm font-medium ${getEstadoColor(objetivo.estado)}`}>
                                                {objetivo.estado}
                                            </span>
                                        </div>

                                        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
                                            <div>
                                                <label className="block text-sm font-medium text-gray-700 mb-1">Estado</label>
                                                <select
                                                    value={objetivo.estado}
                                                    onChange={(e) => actualizarObjetivo(objetivo.id, 'estado', e.target.value)}
                                                    className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 focus:border-transparent text-sm"
                                                    disabled={loading}
                                                >
                                                    {estados.map(estado => (
                                                        <option key={estado} value={estado}>{estado}</option>
                                                    ))}
                                                </select>
                                            </div>

                                            <div>
                                                <label className="block text-sm font-medium text-gray-700 mb-1">Porcentaje (%)</label>
                                                <input
                                                    type="number"
                                                    min="0"
                                                    max="100"
                                                    value={objetivo.porcentaje || 0}
                                                    onChange={(e) => actualizarObjetivo(objetivo.id, 'porcentaje', parseInt(e.target.value))}
                                                    className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 focus:border-transparent text-sm"
                                                    disabled={loading}
                                                />
                                            </div>
                                        </div>

                                        <div className="mb-3">
                                            <div className="w-full bg-gray-200 rounded-full h-2">
                                                <div 
                                                    className="bg-blue-600 h-2 rounded-full transition-all duration-300"
                                                    style={{ width: `${objetivo.porcentaje || 0}%` }}
                                                ></div>
                                            </div>
                                        </div>

                                        <div>
                                            <label className="block text-sm font-medium text-gray-700 mb-1">Observaciones</label>
                                            <textarea
                                                value={objetivo.observaciones || ''}
                                                onChange={(e) => actualizarObjetivo(objetivo.id, 'observaciones', e.target.value)}
                                                rows={2}
                                                className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 focus:border-transparent text-sm"
                                                disabled={loading}
                                            />
                                        </div>
                                    </div>
                                ))}
                            </div>

                            {objetivos.filter(obj => obj.area === currentUser.area).length === 0 && (
                                <div className="text-center py-8 text-gray-500">
                                    <Icons.FileText />
                                    <p className="text-lg mb-2 mt-4">No hay objetivos registrados aún.</p>
                                    <p className="text-sm mb-4">Utilice el formulario de arriba para crear su primer objetivo.</p>
                                    <div className="bg-blue-50 border border-blue-200 rounded-lg p-4 text-sm text-blue-700">
                                        <strong>💡 Información:</strong> Todos los objetivos que registre aquí 
                                        {GOOGLE_SCRIPT_URL === 'DEMO_MODE' 
                                            ? ' se guardan en modo demo y están disponibles para la Vicerrectora durante esta sesión.'
                                            : ' se guardan automáticamente en Google Sheets y están disponibles para la Vicerrectora Marianna Galli.'
                                        }
                                    </div>
                                </div>
                            )}
                        </div>

                        {/* Información del sistema */}
                        <div className={`mt-6 p-4 rounded-lg border ${GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? 'bg-blue-50 border-blue-200' : 'bg-green-50 border-green-200'}`}>
                            <div className="flex items-start">
                                <div className={`w-8 h-8 rounded-full flex items-center justify-center ${GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? 'bg-blue-100' : 'bg-green-100'}`}>
                                    {GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? (
                                        <span className="text-lg">🎭</span>
                                    ) : (
                                        <Icons.Database />
                                    )}
                                </div>
                                <div className="ml-3">
                                    <h3 className={`text-sm font-medium mb-1 ${GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? 'text-blue-900' : 'text-green-900'}`}>
                                        {GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? 'Sistema en Modo Demo' : 'Sistema Conectado a Google Sheets'}
                                    </h3>
                                    <p className={`text-sm ${GOOGLE_SCRIPT_URL === 'DEMO_MODE' ? 'text-blue-700' : 'text-green-700'}`}>
                                        {GOOGLE_SCRIPT_URL === 'DEMO_MODE' 
                                            ? 'Funcionando con todas las características activas. Los datos persisten durante esta sesión y están disponibles para la Vicerrectora en tiempo real.'
                                            : 'Todos sus objetivos, cambios de estado y observaciones se guardan automáticamente en Google Sheets. Los datos están disponibles en tiempo real para la Vicerrectora.'
                                        }
                                    </p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            );
        };

        ReactDOM.render(<Dashboard />, document.getElementById('root'));
    </script>
</body>
</html>
