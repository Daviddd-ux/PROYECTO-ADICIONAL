# PROYECTO-ADICIONAL
import numpy as np

def sensarCultivos(tuplaPosicion):
    i, j = tuplaPosicion
    np.random.seed(i * 10 + j)
    return np.random.randint(0, 10)

def generarPlantacion(tuplaDimensionPlantacion, modo='auto'):
    M, N = tuplaDimensionPlantacion
    plantacion = np.zeros((M, N), dtype=int)

    for i in range(M):
        for j in range(N):
            if modo == 'auto':
                plantacion[i, j] = sensarCultivos((i, j))
            else:
                while True:
                    try:
                        valor = int(input(f"Ingrese cultivos en posición ({i},{j}): "))
                        if valor < 0:
                            raise ValueError
                        plantacion[i, j] = valor
                        break
                    except ValueError:
                        print("Valor inválido. Ingrese un número entero no negativo.")
    return plantacion

def analizarDensidad(plantacion, limite=4):
    return np.where(plantacion >= limite, 'ALTO', 'BAJO')

def reporteCrecimiento(plantacion, densidad):
    promedios_surco = np.round(plantacion.mean(axis=1), 8).tolist()
    posiciones_max = plantacion.argmax(axis=1).tolist()

    alto_mask = densidad == 'ALTO'
    bajo_mask = densidad == 'BAJO'

    promedio_alto = round(np.mean(plantacion[alto_mask]), 5) if np.any(alto_mask) else 0.0
    promedio_bajo = round(np.mean(plantacion[bajo_mask]), 5) if np.any(bajo_mask) else 0.0

    return (promedios_surco, posiciones_max, [promedio_alto, promedio_bajo])

if __name__ == "__main__":
    print("Monitoreo de Cultivos con Dron")

    while True:
        try:
            M = int(input("Ingrese número de surcos (filas): "))
            N = int(input("Ingrese número de parcelas por surco (columnas): "))
            if M <= 0 or N <= 0:
                raise ValueError
            break
        except ValueError:
            print("Ingrese valores enteros positivos.")

    modo = input("¿Desea usar datos simulados del dron? (s/n): ").strip().lower()
    modo = 'auto' if modo == 's' else 'manual'

    plantacion = generarPlantacion((M, N), modo)
    print("\nMatriz de plantación:")
    print(plantacion)

    try:
        limite = int(input("\nIngrese el límite para crecimiento ALTO (default = 4): "))
    except:
        limite = 4

    densidad = analizarDensidad(plantacion, limite)
    print("\nMatriz de densidad:")
    print(densidad)

    reporte = reporteCrecimiento(plantacion, densidad)
    print("\nReporte de crecimiento:")
    print(f"Promedios por surco: {reporte[0]}")
    print(f"Posiciones de máximos por surco: {reporte[1]}")
    print(f"Promedios por crecimiento [ALTO, BAJO]: {reporte[2]}")
