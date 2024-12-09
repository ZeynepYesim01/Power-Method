using System;
using System.Numerics;
using System.Security.Cryptography.X509Certificates;
class PowerMethod
{
    public static void Main()
    {
        // Kullanıcıdan kare matrisin boyutunu alıyoruz
        Console.Write("Enter the matrix size (n x n): ");
        int n = int.Parse(Console.ReadLine());

        // Matrisi kullanıcıdan alıyoruz
        double[,] A = new double[n, n];
        Console.WriteLine("enter the matrix");
        for (int i = 0; i < n; i++)
        {
            for (int j = 0; j < n; j++)
            {
                Console.Write($"A[{i},{j}] = ");
                A[i, j] = double.Parse(Console.ReadLine());
            }
        }

        // Başlangıç vektörünü kullanıcıdan alıyoruz (isteğe bağlı olarak rastgele de yapılabilir)
        double[] x0 = new double[n];
        Console.WriteLine("Enter the initial vector:");
        for (int i = 0; i < n; i++)
        {
            Console.Write($"x[{i}] = ");
            x0[i] = double.Parse(Console.ReadLine());
        }

        // Power Method fonksiyonunu çağırıyoruz
        var result = PowerMethodFunction(A, x0);

        // Sonuçları yazdırıyoruz
        Console.WriteLine($"Dominant Eigenvalue: {result.Item1}");
        Console.WriteLine("Dominant Eigenvector:");
        foreach (var value in result.Item2)
        {
            Console.WriteLine(value);
        }
    }

    public static Tuple<double, double[]> PowerMethodFunction(double[,] A, double[] x0, double tol = 1e-6, int maxIter = 1000)
    {
        int n = A.GetLength(0);
        double[] x = (double[])x0.Clone(); // Başlangıç vektörünü kopyalıyoruz
        double eigenvalue = 0;

        // Başlangıç vektörünü normalize ediyoruz
        Normalize(ref x);

        for (int k = 0; k < maxIter; k++)
        {
            // Matris-vektör çarpımını yapıyoruz
            double[] y = MatrixVectorMultiply(A, x);

            // Vektörü normalize ediyoruz
            Normalize(ref y);

            // Eigenvalue hesaplamak için Rayleigh kesiri kullanıyoruz
            double eigenvalueNew = RayleighQuotient(A, y);

            // Konverjans kontrolü
            if (Math.Abs(eigenvalueNew - eigenvalue) < tol)
            {
                return new Tuple<double, double[]>(eigenvalueNew, y);
            }

            // Değerleri güncelliyoruz
            x = (double[])y.Clone();
            eigenvalue = eigenvalueNew;
        }

        // Eğer maksimum iterasyon sayısında konverjans sağlanmazsa hata mesajı
        throw new Exception("Power method did not converge within the maximum number of iterations.");
    }

    // Matris ile vektörün çarpımı
    public static double[] MatrixVectorMultiply(double[,] A, double[] x)
    {
        int n = A.GetLength(0);
        double[] result = new double[n];

        for (int i = 0; i < n; i++)
        {
            result[i] = 0;
            for (int j = 0; j < n; j++)
            {
                result[i] += A[i, j] * x[j];
            }
        }

        return result;
    }

    // Rayleigh kesirini hesaplamak
    public static double RayleighQuotient(double[,] A, double[] x)
    {
        double numerator = 0, denominator = 0;
        int n = A.GetLength(0);

        for (int i = 0; i < n; i++)
        {
            numerator += x[i] * A[i, i] * x[i];
            for (int j = 0; j < n; j++)
            {
                denominator += x[i] * A[i, j] * x[j];
            }
        }

        return numerator / denominator;
    }

    // Vektörü normalize etmek
    public static void Normalize(ref double[] x)
    {
        double norm = 0;
        foreach (var value in x)
        {
            norm += value * value;
        }
        norm = Math.Sqrt(norm);

        for (int i = 0; i < x.Length; i++)
        {
            x[i] /= norm;
        }
    }
}
