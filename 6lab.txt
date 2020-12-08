vik segment 'code'
assume cs:vik, ds:vik, ss:vik, es:vik
org 100h
begin: jmp main
;-------Данные -------------------
A DT 0BC35FF00ABFF0F1F0FFAh
S DB 0 ; количество нулей
TEN DB ?
ED DB ?
str1 db 10,13,'Number of zeros in a cell: A = $'
str0 db 'Contents of original cell A of 10 bytes:',10,13,'$'
;---------------------------------
main proc near

;-------Команды программы --------
mov bp,0 ; номер байта из десяти --> 0
mov cx,10 ; цикл по количеству байтов

; ***** Начало внешнего цикла ******************
m1: push cx ; сохраняем в стеке счетчик цикла
mov al,byte ptr [A+bp] ;загружаем в al bp-тый байт

; **** внутренний цикл по 8 битам
mov cx,8 ; цикл по количеству битов в байте
m2: test al,00000001b ; проверяем нулевой бит байта
jne m3 ; если он !=0, то продолжаем цикл
add s,1 ; наращиваем счетчик единиц
m3: shr al,1 ; сдвигаем al вправо на один бит
loop m2 ; конец цикла по количеству битов в байте
; конец внутреннего цикла по битам

add bp,1 ; переходим к следующему байту
pop cx ; извлекаем из стека счетчик цикла
loop m1 ; конец цикла по количеству байтов
; ****** конец внешнего цикла

mov ah,09h
lea dx,str0
int 21h

; *** Вывод исходного числа в двоичном виде
mov bp,9
mov cx,10
m23: push cx
mov bl,byte ptr [A+bp]
; распечатка одного байта
mov cx,8
m25: push cx
test bl,10000000b

je m26
mov ah,02h ; там стоит 1. Выводим.
mov dl,'1'
int 21h
jmp m27
m26: ; там стоит 0. Выводим.
mov ah,02h
mov dl,'0'
int 21h
m27: shl bl,1 ; сдвиг влево на 1 бит
pop cx
loop m25
mov ah,02h ; выводим пробел после вывода 8 бит
mov dl,' '
int 21h
sub bp,1 ; переходим к следующему байту
pop cx
loop m23
; *** конец вывода исходного числа в двоичном виде
; ------ вывод результата --------
mov al,s
cbw
mov bl,10
idiv bl ; в al - десятки, в ah = единицы
mov ten,al
mov ed,ah
; ---- вывод строки -----
mov ah,09h
lea dx,str1
int 21h
; ---- отображение символов на экран ----
mov ah,02h ; вывод десятков
mov dl,ten
add dl,30h
int 21h
mov ah,02h
mov dl,ed ; вывод единиц
add dl,30h
int 21h
mov ah,08 ; задержка до нажатия клавиши
int 21h
ret
main endp
vik ends
end begin
