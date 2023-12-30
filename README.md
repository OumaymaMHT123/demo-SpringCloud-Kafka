Partie 1 : (Customer-Service, Inventory-Service, Spring Cloud Gateway, Eureka Discovery)

1.Gestion des clients
- Customer

@Entity
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
}



- application

@SpringBootApplication

        public class CustomerServiceApplication {
	public static void main(String[] args) {
		SpringApplication.run(CustomerServiceApplication.class, args);
	}

	@Bean
	CommandLineRunner start(CustomerRepository customerRepository, RepositoryRestConfiguration restConfiguration) {
		restConfiguration.exposeIdsFor(Customer.class);
		return args -> {
			customerRepository.saveAll(List.of(
					Customer.builder().name("tarhla").email("ladkhan@gmail.com").build(),
					Customer.builder().name("oussama").email("ahmed@gmail.com").build(),
					Customer.builder().name("imane").email("imane@gmail.com").build()
			));
			customerRepository.findAll().forEach(System.out::println);
		};
	}
}

2. Gestion des produits
    - Product

 @Entity
@Data @NoArgsConstructor @AllArgsConstructor @Builder
public class Product {
   @Id
   @GeneratedValue(strategy = GenerationType.IDENTITY)
   private Long id;
   private String name;
   private double price;
   private int quantity;
}   

- Application


@SpringBootApplication
public class InventoryServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(InventoryServiceApplication.class, args);
	}

	@Bean
	CommandLineRunner start(ProductRepository productRepository,
							RepositoryRestConfiguration restConfiguration) {
		restConfiguration.exposeIdsFor(Product.class);
		return args -> {
				productRepository.save(new Product(null,"produit1",5000,3));
			productRepository.save(new Product(null,"produit2",6000,2));
			productRepository.save(new Product(null,"produit3",7000,9));

			productRepository.findAll().forEach(p->{
				System.out.println(p.getName());
			});

		};
	}
}

3. passerelle entre les microservices
- Application

@SpringBootApplication
public class GatewayServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(GatewayServiceApplication.class, args);
	}
	@Bean
	public DiscoveryClientRouteDefinitionLocator definitionLocator(
			ReactiveDiscoveryClient rdc,
			DiscoveryLocatorProperties properties) {
		return new DiscoveryClientRouteDefinitionLocator(rdc, properties);
	}


}

- application.yml

  spring:
  cloud:
    gateway:
      routes:
        - id: r1
          uri: http://localhost:8081/
          predicates:
            - Path=/customers/**

        - id: r2
          uri: http://localhost:8082/
          predicates:
            - Path=/products/**

      globalcors:
        corsConfigurations:
          '[/**]':
            allowedOrigins: "*"
            allowedHeaders: "*"
            allowedMethods:
              - GET
              - POST
              - PUT
              - DELETE


4. Eureka Discovery

   - application.properties
   
server.port=8761
eureka.client.fetch-registry=false
eureka.client.register-with-eureka=false

  - Application

@Bean
@SpringBootApplication
@EnableEurekaServer
public class EurikaDiscoveryApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurikaDiscoveryApplication.class, args);
	}

}


Partie 2 : Billing Service avec Open Feign Rest Client

Dans cette partir, nous avons utilisé Feign  pour deux opérations clés :

  1-Récupérer les informations du client à partir du service Customer
  2-Récupérer la liste des produits à partir du service ProductItem
  <img width="600" alt="image" src="https://github.com/OumaymaMHT123/demo-SpringCloud-Kafka/assets/95369549/e48fcb49-eb70-4dce-a42f-b8aa5f6b5e40">

  Partie 3 : Angular
  <img width="283" alt="image" src="https://github.com/OumaymaMHT123/demo-SpringCloud-Kafka/assets/95369549/2d460460-aca6-4a08-bf99-91a8feb85070">


