let
    // Conservamos los registros originales para mantener la fuente sin modificaciones.
    Origen = Table.FromRows(
        Json.Document(
            Binary.Decompress(
                Binary.FromText(
                    "VZDRTsMgFIZf5aTXawK01XrZuiUaXbK4Gi+aZWEUtUkLDQXj6+wZfIS+mAfUDK+Aj3z8P6dtk747fkhlebJKlB5PRh4nozsnrEYiuJVv2vT+djJS9B6+SvHOf6XDqk0oMnjkk9UT7IwGWgCSWz1OznLRL18Kj5QRggsjLE8JTUkRVIZoq90s4V7xYTljfi98BhdCzhisZy+VsVkGM/OhjRQD7zRspVjO6sfcPT1v6go3N0VkURasPOSp3moD+YOvKf7XzItLS5aSLFj+IagcVnMDN3KGukFSuS6MA38WKZQE5QrRfr+GzaeVRuFMmhricjS7SNnfNK4RvciT4CPcrX1CPISyiAT/ncM3",
                    BinaryEncoding.Base64
                ),
                Compression.Deflate
            )
        ),
        let
            _t = ((type nullable text) meta [Serialized.Text = true])
        in
            type table [
                #"Columna 1" = _t,
                #"Columna 2" = _t,
                #"Columna 3" = _t,
                #"Columna 4" = _t,
                #"Columna 5" = _t
            ]
    ),

    // La primera fila contiene nombres de campos, no una venta.
    Encabezados = Table.PromoteHeaders(
        Origen,
        [PromoteAllScalars = true]
    ),

    // Quitamos espacios externos para evitar diferencias artificiales
    // entre nombres de productos, conservando sus espacios internos.
    LimpiarEspacios = Table.TransformColumns(
        Encabezados,
        {{"nombre_producto", Text.Trim, type text}}
    ),

    // Unificamos las categorías antes de compararlas porque M
    // distingue mayúsculas y minúsculas.
    EstandarizarCategoria = Table.TransformColumns(
        LimpiarEspacios,
        {{"categoria", each Text.Proper(_, "es-AR"), type text}}
    ),

    // Excluimos registros de prueba para que no alteren el análisis.
    // Tras estandarizar, PRUEBA, prueba y Prueba se convierten en Prueba.
    EliminarPruebas = Table.SelectRows(
        EstandarizarCategoria,
        each [categoria] <> "Prueba"
    ),

    // Asignamos tipos explícitos para permitir cálculos y filtros de fecha.
    // en-US interpreta el punto decimal si los precios lo incluyen.
    TiparColumnas = Table.TransformColumnTypes(
        EliminarPruebas,
        {
            {"id_venta", Int64.Type},
            {"nombre_producto", type text},
            {"categoria", type text},
            {"precio", type number},
            {"fecha_venta", type date}
        },
        "en-US"
    )
in
    TiparColumnas
