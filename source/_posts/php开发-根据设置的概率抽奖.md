---
title: php开发-根据设置的概率抽奖
abbrlink: 84d7639f
date: 2024-08-15 18:48:19
tags:
categories:
  - PHP开发
---



# 基础权重抽奖算法

    算法描述：
        1. 获取奖品配置，并计算总权重；
        2. 生成随机数（1-100之间）；
        3. 遍历奖品，确定随机数落在哪个区间；【 具体实现 权重遍历累加 判断】

    为什么这个算法是公平的？
    数学原理正确：每个奖品的中奖概率 = 自身权重 / 总权重
    随机性保证：使用高质量的随机数生成器
    无偏向性：每个权重区间被选中的机会均等
```php
/**
 * 基础权重抽奖算法
 * @param array $prizes 奖品配置 [奖品ID => 权重]
 * @return int|string 中奖奖品ID
 */
function simpleLottery($prizes) {

    // 1. 计算总权重
    $totalWeight = array_sum($prizes);
    
    // 2. 生成随机数 (1-100之间)
    $rand = mt_rand(1, $totalWeight);
    $currentWeight = 0;
    
    // 3. 遍历奖品，确定随机数落在哪个区间
    foreach ($prizes as $prizeId => $weight) {
    
        $currentWeight += $weight;
        
        // 检查随机数是否在当前奖品的区间内
        if ($rand <= $currentWeight) {
            return $prizeId;
        }
    }
    
    return null;
}

// 使用示例
$prizes = [
    '一等奖' => 1,    // 1% 概率
    '二等奖' => 5,    // 5% 概率
    '三等奖' => 10,   // 10% 概率
    '谢谢参与' => 84   // 84% 概率
];

$result = simpleLottery($prizes);
echo "恭喜获得：{$result}";
```



#  抽奖类

```php
class Lottery
{
    private $prizes = [];
    private $totalWeight = 0;
    
    /**
     * 添加奖品
     */
    public function addPrize($id, $name, $weight, $stock = -1) {
        $this->prizes[] = [
            'id' => $id,
            'name' => $name,
            'weight' => $weight,
            'stock' => $stock, // -1 表示无限库存
            'used' => 0
        ];
        $this->totalWeight += $weight;
    }
    
    /**
     * 执行抽奖
     */
    public function draw() {
        if ($this->totalWeight <= 0) {
            return ['success' => false, 'message' => '暂无奖品'];
        }
        
        $rand = mt_rand(1, $this->totalWeight);
        $currentWeight = 0;
        
        foreach ($this->prizes as $prize) {
            // 检查库存
            if ($prize['stock'] > 0 && $prize['used'] >= $prize['stock']) {
                continue;
            }
            
            $currentWeight += $prize['weight'];
            if ($rand <= $currentWeight) {
                $prize['used']++;
                return [
                    'success' => true,
                    'prize' => $prize['name'],
                    'id' => $prize['id']
                ];
            }
        }
        
        return ['success' => false, 'message' => '很遗憾，未中奖'];
    }
    
    /**
     * 获取奖品列表和概率
     */
    public function getPrizeInfo() {
        $info = [];
        foreach ($this->prizes as $prize) {
            $probability = ($prize['weight'] / $this->totalWeight) * 100;
            $info[] = [
                'name' => $prize['name'],
                'probability' => round($probability, 2) . '%',
                'stock' => $prize['stock'] - $prize['used']
            ];
        }
        return $info;
    }
}

// 使用示例
$lottery = new Lottery();
$lottery->addPrize(1, 'iPhone 15', 1, 1);     // 1% 概率，库存1个
$lottery->addPrize(2, 'AirPods', 5, 3);       // 5% 概率，库存3个
$lottery->addPrize(3, '谢谢参与', 94, -1);     // 94% 概率，无限库存

// 查看奖品信息
$prizeInfo = $lottery->getPrizeInfo();
print_r($prizeInfo);

// 抽奖
$result = $lottery->draw();
print_r($result);
```