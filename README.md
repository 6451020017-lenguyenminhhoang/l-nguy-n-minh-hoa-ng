#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define MAX 100

typedef struct 
{
    int ten_san;
    int gio_dat;
    float thgian_thue;
    int loai_san; // 1: Trong nha, 2: Ngoai troi
    int so_luong_nuoc; // sl chai nuoc
    int thue_ao; // 1: Co , 2: Khong
    char sdt[15]; // Sdt thành viên
    char ngay_dat[6]; // Ngày dat san, dinh dang dd/mm
} DatSan;

typedef struct 
{
    DatSan DS[MAX];
    int top;
} Stack;

typedef struct 
{
    char sdt[15]; // Sdt
} ThanhVien;

ThanhVien danhSachThanhVien[MAX];
int soLuongThanhVien = 0;

void init(Stack *stack) 
{
    stack->top = -1;
}

int is_empty(Stack *stack) 
{
    return stack->top == -1;
}

int is_full(Stack *stack) 
{
    return stack->top == MAX - 1;
}

void push(Stack *stack, DatSan DS) 
{
    if (is_full(stack)) 
    {
        printf("Da het san\n");
        return;
    }
    stack->DS[++stack->top] = DS;
}

void push_front(Stack *stack, DatSan DS) 
{
    if (is_full(stack)) 
    {
        printf("Da het san\n");
        return;
    }
    for (int i = stack->top; i >= 0; i--) 
    {
        stack->DS[i + 1] = stack->DS[i];
    }
    stack->DS[0] = DS;
    stack->top++;
}

DatSan pop(Stack *stack) 
{
    if (is_empty(stack)) 
    {
        printf("Khong co lich dat de huy.\n");
        DatSan empty = {-1, -1, -1, -1, -1, -1, "", ""};
        return empty;
    }
    return stack->DS[stack->top--];
}

DatSan pop_front(Stack *stack) 
{
    if (is_empty(stack)) 
    {
        printf("Khong co lich dat de huy.\n");
        DatSan empty = {-1, -1, -1, -1, -1, -1, "", ""};
        return empty;
    }
    DatSan front = stack->DS[0];
    for (int i = 0; i < stack->top; i++) 
    {
        stack->DS[i] = stack->DS[i + 1];
    }
    stack->top--;
    return front;
}

int TinhTien(DatSan DS) 
{
    int tien = 0;
    for (int i = 0; i < DS.thgian_thue; i++) 
    {
        if (DS.gio_dat + i < 16) 
        {
            tien += 200000; 
        } else 
        {
            tien += 300000; 
        }
    }
    if (DS.loai_san == 1) // Neu la san trong nha
    {
        tien = tien * 1.1; // Thêm 10% phu phí
    }
    tien += DS.so_luong_nuoc * 8000; // Thêm chi phí nuoc
    if (DS.thue_ao == 1) // Neu có thuê áo
    {
        tien += 20000; // Thêm chi phí thuê áo
    }
    // Kiem tra neu là thành viên
    for (int i = 0; i < soLuongThanhVien; i++) 
    {
        if (strcmp(DS.sdt, danhSachThanhVien[i].sdt) == 0) 
        {
            tien = tien * 0.95; // Giam 5% cho thành viên
            break;
        }
    }
    return tien;
}

void ThongTin(DatSan DS) 
{
    printf("San so: %d, Gio bat dau: %d, Thoi gian: %f gio, Loai san: %s, So luong nuoc: %d, Thue ao: %s, Ngay dat: %s, Tong tien: %d dong\n",
           DS.ten_san, DS.gio_dat, DS.thgian_thue, DS.loai_san == 1 ? "Trong nha" : "Ngoai troi", DS.so_luong_nuoc, DS.thue_ao == 1 ? "Co" : "Khong", DS.ngay_dat, TinhTien(DS));
}

void XemTatCa(Stack *stack) 
{
    if (is_empty(stack)) 
    {
        printf("Chua co lich dat san nao!\n");
        return;
    }
    for (int i = stack->top; i >= 0; i--) 
    {
        ThongTin(stack->DS[i]);
    }
}

void HienThiSanCungKhungGio(Stack *stack, int gio) 
{
    if (is_empty(stack)) 
    {
        printf("Chua co lich dat san nao!\n");
        return;
    }
    int found = 0;
    for (int i = 0; i <= stack->top; i++) 
    {
        if (stack->DS[i].gio_dat == gio) 
        {
            ThongTin(stack->DS[i]);
            found = 1;
        }
    }
    if (!found) 
    {
        printf("Khong co san nao dat trong khung gio nay.\n");
    }
}

void heapify(DatSan arr[], int n, int i) 
{
    int largest = i; 
    int left = 2 * i + 1;  
    int right = 2 * i + 2; 

    if (left < n && arr[left].gio_dat > arr[largest].gio_dat)
        largest = left;

    if (right < n && arr[right].gio_dat > arr[largest].gio_dat)
        largest = right;

    if (largest != i) 
    {
        DatSan temp = arr[i];
        arr[i] = arr[largest];
        arr[largest] = temp;

        heapify(arr, n, largest);
    }
}

void heapSort(DatSan arr[], int n) 
{
    for (int i = n / 2 - 1; i >= 0; i--)
        heapify(arr, n, i);

    for (int i = n - 1; i >= 0; i--) 
    {
        DatSan temp = arr[0];
        arr[0] = arr[i];
        arr[i] = temp;

        heapify(arr, i, 0);
    }
}

void XemTatCaTheoGio(Stack *stack) 
{
    if (is_empty(stack)) 
    {
        printf("Chua co lich dat san nao!\n");
        return;
    }

    heapSort(stack->DS, stack->top + 1);

    printf("Danh sach dat san theo gio tang dan:\n");
    for (int i = 0; i <= stack->top; i++) 
    {
        ThongTin(stack->DS[i]);
    }
}

void TimKiemSan(Stack *stack, int ma_san, int loai_san) 
{
    if (is_empty(stack)) 
    {
        printf("Chua co lich dat san nao!\n");
        return;
    }
    int found = 0;
    for (int i = 0; i <= stack->top; i++) 
    {
        if (stack->DS[i].ten_san == ma_san && stack->DS[i].loai_san == loai_san) 
        {
            ThongTin(stack->DS[i]);
            found = 1;
        }
    }
    if (!found) 
    {
        printf("Khong tim thay san voi ma san va loai san nay.\n");
    }
}

void HienThiSanTrongNgay(Stack *stack, const char* ngay) 
{
    if (is_empty(stack)) 
    {
        printf("Chua co lich dat san nao!\n");
        return;
    }
    int found = 0;
    for (int i = 0; i <= stack->top; i++) 
    {
        if (strcmp(stack->DS[i].ngay_dat, ngay) == 0) 
        {
            ThongTin(stack->DS[i]);
            found = 1;
        }
    }
    if (!found) 
    {
        printf("Khong co san nao dat trong ngay %s.\n", ngay);
    }
}

void DangKyThanhVien() 
{
    if (soLuongThanhVien >= MAX) 
    {
        printf("Danh sach thanh vien da day.\n");
        return;
    }
    ThanhVien tv;
    printf("Nhap so dien thoai: ");
    scanf("%s", tv.sdt);
        danhSachThanhVien[soLuongThanhVien++] = tv;
    printf("Dang ky thanh cong!\n");
}

int main() 
{
    Stack stack;
    init(&stack);
    int luachon;

    while (1) 
    {
        printf("1. Dat san\n");
        printf("2. Huy san\n");
        printf("3. Hien thi tat ca cac san da dat\n");
        printf("4. Hien thi san da dat theo gio tang dan\n");
        printf("5. Hien thi cac san da dat trong cung khung gio\n");
        printf("6. Them san o dau danh sach\n");
        printf("7. Xoa san o dau danh sach\n");
        printf("8. Them san o cuoi danh sach\n");
        printf("9. Xoa san o cuoi danh sach\n");
        printf("10. Tim kiem san bong bang ma san va loai san\n");
        printf("11. Dang ky thanh vien\n");
        printf("12. Hien thi cac san da dat trong ngay\n");
        printf("13. Thoat\n");
        printf("Chon yeu cau: ");
        scanf("%d", &luachon);

        if (luachon == 1) 
        {
            DatSan DS;
            printf("Nhap so ID cua san (1 toi 30): ");
            scanf("%d", &DS.ten_san);
            printf("Nhap gio bat dau da (5h -> 23h30): ");
            scanf("%d", &DS.gio_dat);
            printf("Nhap thoi gian thue (gio): ");
            scanf("%f", &DS.thgian_thue);
            printf("Nhap loai san (1: Trong nha, 2: Ngoai troi): ");
            scanf("%d", &DS.loai_san);
            printf("Nhap so luong chai nuoc: ");
            scanf("%d", &DS.so_luong_nuoc);
            printf("Thue ao (1: Co, 2: Khong): ");
            scanf("%d", &DS.thue_ao);
            printf("Nhap ngay dat (dd/mm): ");
            scanf("%s", DS.ngay_dat);
            printf("Ban co phai la thanh vien khong? (1: Co, 2: Khong): ");
            int laThanhVien;
            scanf("%d", &laThanhVien);
            if (laThanhVien == 1) 
            {
                printf("Nhap so dien thoai cua ban: ");
                scanf("%s", DS.sdt);
            } 
            else 
            {
                strcpy(DS.sdt, ""); // Không phai thành viên
            }
            push(&stack, DS);
        } 
        else if (luachon == 2) 
        {
            DatSan canceled = pop(&stack);
            if (canceled.ten_san != -1) 
            {
                printf("Da huy san:\n");
                ThongTin(canceled);
            }
        } 
        else if (luachon == 3) 
        {
            XemTatCa(&stack);
        } 
        else if (luachon == 4) 
        {
            XemTatCaTheoGio(&stack);
        } 
        else if (luachon == 5) 
        {
            int gio;
            printf("Nhap gio bat dau: ");
            scanf("%d", &gio);
            HienThiSanCungKhungGio(&stack, gio);
        } 
        else if (luachon == 6) 
        {
            DatSan DS;
            printf("Nhap so ID cua san (1 toi 30): ");
            scanf("%d", &DS.ten_san);
            printf("Nhap gio bat dau da (5h -> 23h30): ");
            scanf("%d", &DS.gio_dat);
            printf("Nhap thoi gian thue (gio): ");
            scanf("%f", &DS.thgian_thue);
            printf("Nhap loai san (1: Trong nha, 2: Ngoai troi): ");
            scanf("%d", &DS.loai_san);
            printf("Nhap so luong chai nuoc: ");
            scanf("%d", &DS.so_luong_nuoc);
            printf("Thue ao (1: Co, 2: Khong): ");
            scanf("%d", &DS.thue_ao);
            printf("Nhap ngay dat (dd/mm): ");
            scanf("%s", DS.ngay_dat);
            printf("Ban co phai la thanh vien khong? (1: Co, 2: Khong): ");
            int laThanhVien;
            scanf("%d", &laThanhVien);
            if (laThanhVien == 1) 
            {
                printf("Nhap so dien thoai cua ban: ");
                scanf("%s", DS.sdt);
            } 
            else 
            {
                strcpy(DS.sdt, ""); // Không phai thành viên
            }
            push_front(&stack, DS);
        } 
        else if (luachon == 7) 
        {
            DatSan canceled = pop_front(&stack);
            if (canceled.ten_san != -1) 
            {
                printf("Da huy san o dau danh sach:\n");
                ThongTin(canceled);
            }
        } 
        else if (luachon == 8) 
        {
            DatSan DS;
            printf("Nhap so ID cua san (1 toi 30): ");
            scanf("%d", &DS.ten_san);
            printf("Nhap gio bat dau da (5h -> 23h30): ");
            scanf("%d", &DS.gio_dat);
            printf("Nhap thoi gian thue (gio): ");
            scanf("%f", &DS.thgian_thue);
            printf("Nhap loai san (1: Trong nha, 2: Ngoai troi): ");
            scanf("%d", &DS.loai_san);
            printf("Nhap so luong chai nuoc: ");
            scanf("%d", &DS.so_luong_nuoc);
            printf("Thue ao (1: Co, 2: Khong): ");
            scanf("%d", &DS.thue_ao);
            printf("Nhap ngay dat (dd/mm): ");
            scanf("%s", DS.ngay_dat);
            printf("Ban co phai la thanh vien khong? (1: Co, 2: Khong): ");
            int laThanhVien;
            scanf("%d", &laThanhVien);
            if (laThanhVien == 1) 
            {
                printf("Nhap so dien thoai cua ban: ");
                scanf("%s", DS.sdt);
            } 
            else 
            {
                strcpy(DS.sdt, ""); // Không phai thành viên
            }
            push(&stack, DS);
        } 
        else if (luachon == 9) 
        {
            DatSan canceled = pop(&stack);
            if (canceled.ten_san != -1) 
            {
                printf("Da huy san o cuoi danh sach:\n");
                ThongTin(canceled);
            }
        } 
        else if (luachon == 10) 
        {
            int ma_san, loai_san;
            printf("Nhap ma san: ");
            scanf("%d", &ma_san);
            printf("Nhap loai san (1: Trong nha, 2: Ngoai troi): ");
            scanf("%d", &loai_san);
            TimKiemSan(&stack, ma_san, loai_san);
        } 
        else if (luachon == 11) 
        {
            DangKyThanhVien();
        } 
        else if (luachon == 12) 
        {
            char ngay[6];
            printf("Nhap ngay (dd/mm): ");
            scanf("%s", ngay);
            HienThiSanTrongNgay(&stack, ngay);
        } 
        else if (luachon == 13) 
        {
            printf("Thoat chuong trinh.\n");
            break;
        } 
        else 
        {
            printf("Lua chon khong hop le (vui long nhap 1 -> 13).\n");
        }
    }

    return 0;
}
