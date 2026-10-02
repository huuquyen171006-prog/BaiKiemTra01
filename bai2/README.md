<img width="1366" height="975" alt="Screenshot 2026-10-02 141055" src="https://github.com/user-attachments/assets/a257caad-9582-4319-987d-ce98bae882fb" />
<img width="1586" height="876" alt="Screenshot 2026-10-02 141116" src="https://github.com/user-attachments/assets/d22c1eb2-6e7d-4d74-bb56-f24b12498b78" />
<img width="1499" height="448" alt="Screenshot 2026-10-02 141124" src="https://github.com/user-attachments/assets/9652b992-b5e6-4378-b61b-ee76513043b5" />

using System;
namespace AutoSpeedOOP
{
    public abstract class PhuongTien
    {
        private string _maPT;
        private string _tenHang;
        private int _namSanXuat;
        private decimal _giaGoc;

        public string MaPT
        {
            get { return _maPT; }
            set
            {
                if (string.IsNullOrWhiteSpace(value))
                    _maPT = "PT000";
                else
                    _maPT = value.Trim();
            }
        }
        public string TenHang
        {
            get { return _tenHang; }
            set
            {
                if (string.IsNullOrWhiteSpace(value))
                    throw new ArgumentException("Tên hãng không được để trống!");

                _tenHang = value.Trim();
            }
        }
        public int NamSanXuat
        {
            get { return _namSanXuat; }
            set
            {
                if (value < 1900 || value > DateTime.Now.Year)
                    throw new ArgumentException("Năm sản xuất không hợp lệ!");

                _namSanXuat = value;
            }
        }
        public decimal GiaGoc
        {
            get { return _giaGoc; }
            set
            {
                if (value <= 0)
                    throw new ArgumentException("Giá gốc phải lớn hơn 0!");

                _giaGoc = value;
            }
        }
        public PhuongTien(
            string maPT,
            string tenHang,
            int namSanXuat,
            decimal giaGoc)
        {
            MaPT = maPT;
            TenHang = tenHang;
            NamSanXuat = namSanXuat;
            GiaGoc = giaGoc;
        }
        public abstract decimal TinhGiaLanBanh();
        public virtual string GetInfo()
        {
            return $"Mã PT: {MaPT} | " +
                   $"Hãng: {TenHang} | " +
                   $"Năm SX: {NamSanXuat} | " +
                   $"Giá gốc: {GiaGoc:N0} VNĐ";
        }
    }
}
using System;

namespace AutoSpeedOOP
{
    public class OTo : PhuongTien
    {
        private int _soChoNgoi;
        private double _dungTichDongCo;

        public int SoChoNgoi
        {
            get { return _soChoNgoi; }
            set
            {
                if (value <= 0)
                    throw new ArgumentException("Số chỗ ngồi phải lớn hơn 0!");

                _soChoNgoi = value;
            }
        }
        public double DungTichDongCo
        {
            get { return _dungTichDongCo; }
            set
            {
                if (value <= 0)
                    throw new ArgumentException("Dung tích động cơ phải lớn hơn 0!");

                _dungTichDongCo = value;
            }
        }
        public OTo(
            string maPT,
            string tenHang,
            int namSanXuat,
            decimal giaGoc,
            int soChoNgoi,
            double dungTichDongCo)
            : base(maPT, tenHang, namSanXuat, giaGoc)
        {
            SoChoNgoi = soChoNgoi;
            DungTichDongCo = dungTichDongCo;
        }
        public override decimal TinhGiaLanBanh()
        {
            if (SoChoNgoi <= 9)
            {
                decimal lePhiTruocBa = GiaGoc * 0.12m;
                decimal thueTieuThuDacBiet = GiaGoc * 0.30m;

                return GiaGoc
                       + lePhiTruocBa
                       + thueTieuThuDacBiet;
            }
            else
            {
                decimal lePhiTruocBa = GiaGoc * 0.10m;

                return GiaGoc + lePhiTruocBa;
            }
        }
        public override string GetInfo()
        {
            return base.GetInfo()
                   + $" | Số chỗ: {SoChoNgoi}"
                   + $" | Động cơ: {DungTichDongCo}L";
        }
    }
}
using System;

namespace AutoSpeedOOP
{
    public class XeMay : PhuongTien
    {
        private int _dungTichXylanh;

        public int DungTichXylanh
        {
            get { return _dungTichXylanh; }
            set
            {
                if (value <= 0)
                    throw new ArgumentException("Dung tích xy lanh phải lớn hơn 0!");

                _dungTichXylanh = value;
            }
        }
        public XeMay(
            string maPT,
            string tenHang,
            int namSanXuat,
            decimal giaGoc,
            int dungTichXylanh)
            : base(maPT, tenHang, namSanXuat, giaGoc)
        {
            DungTichXylanh = dungTichXylanh;
        }
        public override decimal TinhGiaLanBanh()
        {
            if (DungTichXylanh < 175)
            {
                return GiaGoc + GiaGoc * 0.02m;
            }
            else
            {
                return GiaGoc + GiaGoc * 0.05m;
            }
        }
        public override string GetInfo()
        {
            return base.GetInfo()
                   + $" | Dung tích xy lanh: {DungTichXylanh}cc";
        }
    }
}
using System;
using System.Collections.Generic;
using System.Linq;

namespace AutoSpeedOOP
{
    public class QuanLyPhuongTien
    {
        private List<PhuongTien> danhSach;

        public QuanLyPhuongTien()
        {
            danhSach = new List<PhuongTien>();
        }

        public void AddPhuongTien(PhuongTien pt)
        {
            danhSach.Add(pt);
        }

        public void DisplayAll()
        {
            if (danhSach.Count == 0)
            {
                Console.WriteLine("Danh sách phương tiện đang trống!");
                return;
            }

            Console.WriteLine();
            Console.WriteLine("=========== DANH SÁCH PHƯƠNG TIỆN ===========");

            foreach (PhuongTien pt in danhSach)
            {
                Console.WriteLine(pt.GetInfo());

                Console.WriteLine(
                    "Giá lăn bánh: {0:N0} VNĐ",
                    pt.TinhGiaLanBanh());

                Console.WriteLine(
                    "------------------------------------------------");
            }
        }

        public PhuongTien FindMaxGiaLanBanh()
        {
            if (danhSach.Count == 0)
                return null;

            PhuongTien max = danhSach[0];

            foreach (PhuongTien pt in danhSach)
            {
                if (pt.TinhGiaLanBanh() >
                    max.TinhGiaLanBanh())
                {
                    max = pt;
                }
            }

            return max;
        }

        public List<PhuongTien> SearchByName(string keyword)
        {
            return danhSach
                .Where(pt =>
                    pt.TenHang.IndexOf(
                        keyword,
                        StringComparison.OrdinalIgnoreCase) >= 0)
                .ToList();
        }
    }
}
using System;
using System.Collections.Generic;
using System.Text;

namespace AutoSpeedOOP
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Console.OutputEncoding = Encoding.UTF8;
            Console.InputEncoding = Encoding.UTF8;

            QuanLyPhuongTien ql = new QuanLyPhuongTien();

            int chon;

            do
            {
                Console.WriteLine();
                Console.WriteLine("==============================================");
                Console.WriteLine("       QUẢN LÝ PHƯƠNG TIỆN AUTOSPEED");
                Console.WriteLine("==============================================");
                Console.WriteLine("1. Thêm ô tô");
                Console.WriteLine("2. Thêm xe máy");
                Console.WriteLine("3. Hiển thị danh sách");
                Console.WriteLine("4. Tìm phương tiện giá lăn bánh cao nhất");
                Console.WriteLine("5. Tìm kiếm theo tên hãng");
                Console.WriteLine("0. Thoát");
                Console.WriteLine("==============================================");

                chon = NhapSoNguyen("Nhập lựa chọn: ");

                switch (chon)
                {
                    case 1:
                        NhapOTo(ql);
                        break;

                    case 2:
                        NhapXeMay(ql);
                        break;

                    case 3:
                        ql.DisplayAll();
                        break;

                    case 4:
                        TimGiaCaoNhat(ql);
                        break;

                    case 5:
                        TimTheoHang(ql);
                        break;

                    case 0:
                        Console.WriteLine("Đã thoát chương trình!");
                        break;

                    default:
                        Console.WriteLine("Lựa chọn không hợp lệ!");
                        break;
                }

            } while (chon != 0);

            Console.ReadKey();
        }

        static void NhapOTo(QuanLyPhuongTien ql)
        {
            Console.WriteLine();
            Console.WriteLine("========== NHẬP Ô TÔ ==========");

            try
            {
                Console.Write("Mã phương tiện: ");
                string maPT = Console.ReadLine();

                Console.Write("Tên hãng: ");
                string tenHang = Console.ReadLine();

                int namSX =
                    NhapSoNguyen("Năm sản xuất: ");

                decimal giaGoc =
                    NhapDecimal("Giá gốc: ");

                int soCho =
                    NhapSoNguyen("Số chỗ ngồi: ");

                double dungTich =
                    NhapDouble("Dung tích động cơ (L): ");

                OTo oto = new OTo(
                    maPT,
                    tenHang,
                    namSX,
                    giaGoc,
                    soCho,
                    dungTich);

                ql.AddPhuongTien(oto);

                Console.WriteLine();
                Console.WriteLine("Thêm ô tô thành công!");

                Console.WriteLine(oto.GetInfo());

                Console.WriteLine(
                    "Giá lăn bánh: {0:N0} VNĐ",
                    oto.TinhGiaLanBanh());
            }
            catch (ArgumentException ex)
            {
                Console.WriteLine(
                    "Lỗi: " + ex.Message);
            }
        }

        static void NhapXeMay(QuanLyPhuongTien ql)
        {
            Console.WriteLine();
            Console.WriteLine("========== NHẬP XE MÁY ==========");

            try
            {
                Console.Write("Mã phương tiện: ");
                string maPT = Console.ReadLine();

                Console.Write("Tên hãng: ");
                string tenHang = Console.ReadLine();

                int namSX =
                    NhapSoNguyen("Năm sản xuất: ");

                decimal giaGoc =
                    NhapDecimal("Giá gốc: ");

                int dungTich =
                    NhapSoNguyen("Dung tích xy lanh (cc): ");

                XeMay xeMay = new XeMay(
                    maPT,
                    tenHang,
                    namSX,
                    giaGoc,
                    dungTich);

                ql.AddPhuongTien(xeMay);

                Console.WriteLine();
                Console.WriteLine("Thêm xe máy thành công!");

                Console.WriteLine(xeMay.GetInfo());

                Console.WriteLine(
                    "Giá lăn bánh: {0:N0} VNĐ",
                    xeMay.TinhGiaLanBanh());
            }
            catch (ArgumentException ex)
            {
                Console.WriteLine(
                    "Lỗi: " + ex.Message);
            }
        }

        static void TimGiaCaoNhat(
            QuanLyPhuongTien ql)
        {
            Console.WriteLine();
            Console.WriteLine(
                "===== PHƯƠNG TIỆN GIÁ LĂN BÁNH CAO NHẤT =====");

            PhuongTien max =
                ql.FindMaxGiaLanBanh();

            if (max == null)
            {
                Console.WriteLine(
                    "Danh sách đang trống!");
                return;
            }

            Console.WriteLine(max.GetInfo());

            Console.WriteLine(
                "Giá lăn bánh: {0:N0} VNĐ",
                max.TinhGiaLanBanh());
        }

        static void TimTheoHang(
            QuanLyPhuongTien ql)
        {
            Console.WriteLine();

            Console.Write(
                "Nhập tên hãng cần tìm: ");

            string keyword =
                Console.ReadLine();

            List<PhuongTien> ketQua =
                ql.SearchByName(keyword);

            if (ketQua.Count == 0)
            {
                Console.WriteLine(
                    "Không tìm thấy phương tiện!");
                return;
            }

            Console.WriteLine();
            Console.WriteLine(
                "========== KẾT QUẢ TÌM KIẾM ==========");

            foreach (PhuongTien pt in ketQua)
            {
                Console.WriteLine(pt.GetInfo());

                Console.WriteLine(
                    "Giá lăn bánh: {0:N0} VNĐ",
                    pt.TinhGiaLanBanh());

                Console.WriteLine(
                    "--------------------------------------");
            }
        }

        static int NhapSoNguyen(
            string thongBao)
        {
            int n;

            while (true)
            {
                Console.Write(thongBao);

                if (int.TryParse(
                    Console.ReadLine(), out n))
                {
                    return n;
                }

                Console.WriteLine(
                    "Vui lòng nhập số nguyên!");
            }
        }

        static decimal NhapDecimal(
            string thongBao)
        {
            decimal n;

            while (true)
            {
                Console.Write(thongBao);

                if (decimal.TryParse(
                    Console.ReadLine(), out n))
                {
                    return n;
                }

                Console.WriteLine(
                    "Vui lòng nhập số hợp lệ!");
            }
        }

        static double NhapDouble(
            string thongBao)
        {
            double n;

            while (true)
            {
                Console.Write(thongBao);

                if (double.TryParse(
                    Console.ReadLine(), out n))
                {
                    return n;
                }

                Console.WriteLine(
                    "Vui lòng nhập số hợp lệ!");
            }
        }
    }
}
