---
title: "SpringBoot SpringSecurity static resource 설정"
date: 2019-12-10 12:00:00 -0400
categories: Spring, SpringBoot, Resource, static
---

1. Dir 구조
 - 

2. WebConfig 설정 
```java
@Configuration
@EnableWebMvc

public class WebConfig implements WebMvcConfigurer {

    @Bean
    public InternalResourceViewResolver resolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setViewClass(JstlView.class);
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        return resolver;
    }

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry
                .addResourceHandler("/resources/**")
                .addResourceLocations("classpath:/META-INF/resources/", "classpath:/resources/","classpath:/resource/",
                        "classpath:/static/", "classpath:/public/")
                .setCachePeriod(20);
    }
}
```

3. SpringSecurity 설정
```java
 @Override
    public void configure(WebSecurity web) throws Exception
    {
        web.ignoring().antMatchers("/resources/**","images/**", "css/**","/script/**", "image/**", "/fonts/**", "lib/**");
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception
    {
        http
                .authorizeRequests()
                    .antMatchers("/", "/login","/service","/resources/**","/create").permitAll()
                    .antMatchers("/admin").hasRole("ADMIN")
                    .anyRequest().authenticated()
                    .and()
```

