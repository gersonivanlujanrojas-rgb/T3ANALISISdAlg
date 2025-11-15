[t3Analisis.py](https://github.com/user-attachments/files/23557290/t3Analisis.py)


energiaInicial = 18

laberinto = [
    [1,  1,  1,  1, 99, 1, 1, 1, 1],
    [1, 99, 99,  1, 99, 1, 99, 1, 99],
    [1,  1, 99,  1,  1, 1, 99, 1, 99],
    [99, 1, 99,  1, 99, 99, 99, 1, 99],
    [1,  1, 99, -1,  1, 1,  1, 3, 99],
    [-2,99, 99,  1, 99, 99, 99, 1, 1],
    [1, 99, 1, -1,  1, 1,  1, 1, 99],
    [1, 99, 99, 99, 99, 2, 99, 1, 99],
    [1,  1,  3,  1,  1, 1, 99, 1, 1]
]


inicio = (0, 8)
final  = (8, 0)   # celda roja


def mostrarLaberinto(lab):
    print("Laberinto: ")
    for fila in lab:
        print(" ".join(f"{v:3d}" for v in fila))
    print()


def construirCaminoSalida(lab, camino, inicio, final):
    filas = len(lab)
    cols = len(lab[0])

    m = [[f"{lab[i][j]}" for j in range(cols)] for i in range(filas)]

    for (i, j) in camino:
        m[i][j] = '*'

    ix, iy = inicio
    fx, fy = final
    m[ix][iy] = 'I'
    m[fx][fy] = 'F'

    return m


def mostrarCaminoSalida(m):
    print("Camino de salida: ")
    for fila in m:
        print(" ".join(f"{v:>3}" for v in fila))
    print()


def resolverLaberinto(lab, inicio, final, energiaInicial):
    filas = len(lab)
    cols = len(lab[0])

    visitado = [[False]*cols for _ in range(filas)]
    camino = []

    movimientos = [
        (0, -1),
        (1,  0),
        (-1, 0), 
        (0,  1)
    ]

    def backtracking(x, y, energiaActual):
        if (x, y) == final:
            return True, energiaActual

        for dx, dy in movimientos:
            nx, ny = x + dx, y + dy

            if nx < 0 or nx >= filas or ny < 0 or ny >= cols:
                continue

            if lab[nx][ny] == 99:
                continue

            if visitado[nx][ny]:
                continue

            valor = lab[nx][ny]

            if (nx, ny) == final:
                nuevaEnergia = energiaActual
            else:
                nuevaEnergia = energiaActual - valor

            if nuevaEnergia < 0:
                continue

            visitado[nx][ny] = True
            camino.append((nx, ny))

            exito, energiaRestante = backtracking(nx, ny, nuevaEnergia)
            if exito:
                return True, energiaRestante

            camino.pop()
            visitado[nx][ny] = False

        return False, energiaActual

    sx, sy = inicio
    visitado[sx][sy] = True
    camino.append(inicio)

    exito, energiaRestante = backtracking(sx, sy, energiaInicial)
    if not exito:
        camino.clear()

    return exito, camino, energiaRestante


def main():
    mostrarLaberinto(laberinto)

    exito, camino, energiaRestante = resolverLaberinto(
        laberinto, inicio, final, energiaInicial
    )

    if exito:
        print("Se encontró un camino desde el inicio hasta el final. ")
        print(f"Energía inicial:   {energiaInicial} " )
        print(f"Energía restante:  {energiaRestante} ")
        print(f"Cantidad de pasos: {len(camino)-1} ")
        
        matriz_camino = construirCaminoSalida(laberinto, camino, inicio, final)
        mostrarCaminoSalida(matriz_camino)

    else:
        print("Laberinto sin salida D: ")


if __name__ == "__main__":
    main()
