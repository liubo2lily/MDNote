# 0 前言

GPIO有新旧两套API，gpiod(descriptor)和gpio，两者可以通过下列函数相互转换。gpiod的优势在于支持devm。

```c
int desc_to_gpio(const struct gpio_desc *desc);
struct gpio_desc *gpio_to_desc(unsigned gpio);
```

# 1 注册流程暂存

```c
static int gpio_key_probe(struct platform_device *pdev)
{
    struct device_node *node = pdev->dev.of_node;
    int count;
	int i;
    enum of_gpio_flags flag;
    unsigned flags = GPIOF_IN;
	
    count = of_gpio_count(node);
    
    gpio_keys_100ask = kzalloc(sizeof(struct gpio_key) * count, GFP_KERNEL);
    
    for (i = 0; i < count; i++)
	{
		gpio_keys_100ask[i].gpio = of_get_gpio_flags(node, i, &flag);
		//...
		gpio_keys_100ask[i].gpiod = gpio_to_desc(gpio_keys_100ask[i].gpio);
		gpio_keys_100ask[i].flag = flag & OF_GPIO_ACTIVE_LOW;
		devm_gpiod_get(struct device * dev, const char * con_id, enum gpiod_flags flags)
		if (flag & OF_GPIO_ACTIVE_LOW)
			flags |= GPIOF_ACTIVE_LOW;

		err = devm_gpio_request_one(&pdev->dev, gpio_keys_100ask[i].gpio, flags, NULL);
	}
    
}
```
