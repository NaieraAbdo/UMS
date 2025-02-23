# استخدم صورة .NET 8 كبيئة بناء
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build-env
WORKDIR /app

# انسخ الملفات واسترجع الـ dependencies
COPY . ./
RUN dotnet restore

# ابني المشروع
RUN dotnet publish -c Release -o out

# استخدم صورة .NET Runtime فقط للتشغيل (أخف وأسرع)
FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build-env /app/out .

# تحديد المنفذ
ENV ASPNETCORE_URLS=http://+:10000
EXPOSE 10000

# تشغيل التطبيق
CMD ["dotnet", "UMS.dll"]
