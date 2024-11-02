using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Text.Json;
using System.Threading.Tasks;

namespace CafeFinder
{
    // Модель кафе
    public class Cafe
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Address { get; set; }
        public int FreeTables { get; set; }
        public string ImageUrl { get; set; }
    }

    // Модель ответа от API
    public class CafeApiResponse
    {
        public List<Cafe> Cafes { get; set; }
    }

    // Класс клиента
    public class CafeClient
    {
        private readonly HttpClient _httpClient;
        private readonly string _apiUrl;

        public CafeClient(string apiUrl)
        {
            _apiUrl = apiUrl;
            _httpClient = new HttpClient();
        }

        // Метод для получения свободных мест в кафе
        public async Task<CafeApiResponse> GetFreeCafesAsync()
        {
            var response = await _httpClient.GetAsync(_apiUrl);

            if (response.IsSuccessStatusCode)
            {
                var json = await response.Content.ReadAsStringAsync();
                return JsonSerializer.Deserialize<CafeApiResponse>(json);
            }
            else
            {
                throw new Exception($"Ошибка при получении данных: {response.StatusCode}");
            }
        }
    }

    // Пример использования клиента
    class Program
    {
        static async Task Main(string[] args)
        {
            // Замените на фактический URL API
            var apiUrl = "https://api.example.com/cafes";

            var cafeClient = new CafeClient(apiUrl);

            try
            {
                var cafes = await cafeClient.GetFreeCafesAsync();

                Console.WriteLine("Доступные кафе:");

                foreach (var cafe in cafes.Cafes)
                {
                    Console.WriteLine($"Название: {cafe.Name}");
                    Console.WriteLine($"Адрес: {cafe.Address}");
                    Console.WriteLine($"Свободные столики: {cafe.FreeTables}");
                    Console.WriteLine();
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Ошибка: {ex.Message}");
            }
        }
    }
}
