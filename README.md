import java.util.Scanner;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class ConversorMonedas {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.println("Bienvenido al conversor de monedas");
        System.out.println("Por favor, ingresa la cantidad a convertir:");
        double cantidad = scanner.nextDouble();

        System.out.println("Selecciona la moneda de origen:");
        System.out.println("1. ARS - Peso Argentino");
        System.out.println("2. BOB - Boliviano Boliviano");
        System.out.println("3. BRL - Real Brasileño");
        System.out.println("4. CLP - Peso Chileno");
        int opcionOrigen = scanner.nextInt();

        System.out.println("Selecciona la moneda de destino:");
        System.out.println("1. ARS - Peso Argentino");
        System.out.println("2. BOB - Boliviano Boliviano");
        System.out.println("3. BRL - Real Brasileño");
        System.out.println("4. CLP - Peso Chileno");
        int opcionDestino = scanner.nextInt();

        double resultado = 0.0;

        try {
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest request = HttpRequest.newBuilder()
                    .uri(URI.create("https://v6.exchangerate-api.com/v6/41a013ca560ccb658c9014f9/latest/USD"))
                    .build();

            HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

            if (response.statusCode() == 200) {
                String responseBody = response.body();

                double arsToUsd = obtenerTipoCambio(responseBody, "ARS");
                double bobToUsd = obtenerTipoCambio(responseBody, "BOB");
                double brlToUsd = obtenerTipoCambio(responseBody, "BRL");
                double clpToUsd = obtenerTipoCambio(responseBody, "CLP");

                switch (opcionOrigen) {
                    case 1:
                        resultado = cantidad / arsToUsd;
                        break;
                    case 2:
                        resultado = cantidad / bobToUsd;
                        break;
                    case 3:
                        resultado = cantidad / brlToUsd;
                        break;
                    case 4:
                        resultado = cantidad / clpToUsd;
                        break;
                    default:
                        System.out.println("Opción de moneda de origen no válida");
                        return;
                }

                switch (opcionDestino) {
                    case 1:
                        resultado *= arsToUsd;
                        break;
                    case 2:
                        resultado *= bobToUsd;
                        break;
                    case 3:
                        resultado *= brlToUsd;
                        break;
                    case 4:
                        resultado *= clpToUsd;
                        break;
                    default:
                        System.out.println("Opción de moneda de destino no válida");
                        return;
                }

                System.out.println("El resultado de la conversión es: " + resultado);
            } else {
                System.out.println("No se pudo obtener los datos de tipo de cambio. Código de estado: " + response.statusCode());
            }
        } catch (Exception e) {
            System.out.println("Error al procesar la solicitud: " + e.getMessage());
        }
    }

    private static double obtenerTipoCambio(String responseBody, String moneda) {
        int index = responseBody.indexOf(moneda);
        int startIndex = responseBody.indexOf(":", index) + 1;
        int endIndex = responseBody.indexOf(",", startIndex);
        String tipoCambioString = responseBody.substring(startIndex, endIndex).trim();
        return Double.parseDouble(tipoCambioString);
    }
}# challengeconversor
