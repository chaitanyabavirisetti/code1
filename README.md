# code1
to check the 
[12:26] Janaki Annam







this is the main branch code



user

package com.example.newreg.Entity;

import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;

import javax.persistence.*;
import javax.persistence.Entity;
@Entity
@AllArgsConstructor
@NoArgsConstructor
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    @Column(name = "user_name")
    private String username;

    @Column(name = "password")
    private String password;

    // Getters and setters

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
 

userservice

package com.example.newreg.Service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import com.example.newreg.DTO.UserDto;
import com.example.newreg.Entity.User;
import com.example.newreg.Repo.UserRepository;

@Service
public class UserService {

    private final UserRepository userRepository;

    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User createUser(UserDto userDto) {
        User user = new User();
        user.setUsername(userDto.getUsername());
        user.setPassword(userDto.getPassword());


        return userRepository.save(user);
    }



}
 

userDto

package com.example.newreg.DTO;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

public class UserDto {

    private String username;
    private String password;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public List<String> validate() {
        List<String> errors = new ArrayList<>();

        if (username == null || username.isEmpty()) {
            errors.add("Username is required");
        }

        if (password == null || password.isEmpty()) {
            errors.add("Password is required");
        } else if (password.length() < 6) {
            errors.add("Password should be at least 6 characters");
        }

        return errors;
    }
}
 

user repository

package com.example.newreg.Repo;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.stereotype.Repository;
import com.example.newreg.Entity.User;

import java.util.Optional;

@Repository
@EnableJpaRepositories
public interface UserRepository extends JpaRepository<User, Long> {

    Optional<User> findByUsername(String username);

    // Additional repository methods can be added as needed
}
 

apllicaton.properties


# Server port
server.port=8089

# Database configuration
spring.datasource.url=jdbc:mysql://localhost:3306/srinivasa_schema
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# Hibernate configuration
spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
spring.jpa.hibernate.ddl-auto=create
spring.jpa.show-sql=true

# Logging configuration

logging.level.com.example=DEBUG

logging.level.org.springframework=INFO

usercontroller

package com.example.newreg.Controller;

import com.example.newreg.DTO.UserDto;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import com.example.newreg.Service.UserService;

@RestController
@RequestMapping("/api")
public class UserController {

    @Autowired
    private UserService userService;

    @PostMapping("/registered")
    public ResponseEntity<String> register(@RequestBody UserDto userDto) {
        // Validate and save the user
        userService.createUser(userDto);

        return ResponseEntity.ok("Registration successful");
    }
}
 

main application

package com.example.newreg;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;

@SpringBootApplication

@ComponentScan({"main.Controllers", "main.Repo"})
@EnableJpaRepositories("main.Repo")

public class NewregApplication {

   public static void main(String[] args) {
      SpringApplication.run(NewregApplication.class, args);
   }

}
 


