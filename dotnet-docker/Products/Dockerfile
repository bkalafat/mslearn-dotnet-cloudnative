# .net 8 docker imajını "build" ismi ile belirliyoruz
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build 

# Çalışma dizinini /DataEntities olarak belirliyoruz
WORKDIR /DataEntities
# Sadece DataEntities projesini kopyala
COPY "DataEntities/DataEntities.csproj" .
# Bağımlılıkları (nuget paketleri) önceden yükle
RUN dotnet restore
# DataEntities klasörünü tamamen(.) konteyner içine kopyala
COPY "DataEntities" .
# Release modunda derle (compile) ve derleme çıktısını (output) /app dizinine kopyala
RUN dotnet publish -c release -o /app

# Çalışma dizinini /src olarak belirle
WORKDIR /src
# Sadece Products proje dosyasını indir böylece tümünü kopyalamadan önce build işlemi hızlanır
COPY Products/Products.csproj .
RUN dotnet restore
# Şimdi tüm klasörü kopyaladık
COPY Products .
# Uygulamayı release modunda compile edip outputu da /app dizinine koyar
RUN dotnet publish -c release -o /app
# Şimdi .NET SDK yerine ASP.NET Core Runtime kullan (daha hafif; run time için ideal)
FROM mcr.microsoft.com/dotnet/aspnet:8.0
# Çalışma dizini olarak /app klasörüne geç
WORKDIR /app
# HTTP isteklerini 80. porttan kabul et
EXPOSE 80
# HTTPS isteklerini 443. porttan kabul et
EXPOSE 443
# build önceki build'deki /app içeriğini yeni imajın /app dizinine kopyalar
COPY --from=build /app .
# Konteyner başladığında Products uygulamasını çalıştır
ENTRYPOINT ["dotnet", "Products.dll"]