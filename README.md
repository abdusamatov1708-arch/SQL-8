# SQL-8
SELECT
    t.sinf,
    COUNT(DISTINCT t.id) AS talabalar_soni,
    ROUND(AVG(b.baho), 2) AS o_rtacha_baho,
    MAX(b.baho) AS maksimal_baho,
    MIN(b.baho) AS minimal_baho
FROM talabalar t
LEFT JOIN baholar b ON t.id = b.talaba_id
GROUP BY t.sinf
ORDER BY o_rtacha_baho DESC;

SELECT
    f.nomi AS fan_nomi,
    COUNT(b.id) AS baholar_soni,
    ROUND(AVG(b.baho), 2) AS o_rtacha_baho
FROM fanlar f
JOIN baholar b ON f.id = b.fan_id
GROUP BY f.id, f.nomi
HAVING COUNT(b.id) >= 5 AND AVG(b.baho) > 70
ORDER BY o_rtacha_baho DESC;

SELECT
    sub.fan_nomi,
    sub.talaba_ismi,
    sub.o_rtacha_baho
FROM (
    SELECT
        f.nomi AS fan_nomi,
        t.ism AS talaba_ismi,
        ROUND(AVG(b.baho), 2) AS o_rtacha_baho,
        ROW_NUMBER() OVER(PARTITION BY f.id ORDER BY AVG(b.baho) DESC) as rn
    FROM fanlar f
    JOIN baholar b ON f.id = b.fan_id
    JOIN talabalar t ON b.talaba_id = t.id
    GROUP BY f.id, f.nomi, t.id, t.ism
) sub
WHERE sub.rn = 1;

SELECT
    t.id AS talaba_id,
    t.ism AS talaba_ismi,
    t.sinf
FROM talabalar t
LEFT JOIN baholar b ON t.id = b.talaba_id
WHERE b.baho IS NULL;

SELECT
    t.ism AS talaba_ismi,
    MIN(b.baho) AS eng_past_bahosi,
    ROUND(AVG(b.baho), 2) AS o_rtacha_baho
FROM talabalar t
JOIN baholar b ON t.id = b.talaba_id
GROUP BY t.id, t.ism
HAVING MIN(b.baho) >= 85;

SELECT
    t.ism AS talaba_ismi,
    MIN(b.baho) AS eng_past_bahosi,
    ROUND(AVG(b.baho), 2) AS o_rtacha_baho
FROM talabalar t
JOIN baholar b ON t.id = b.talaba_id
GROUP BY t.id, t.ism
HAVING MIN(b.baho) >= 85;

SELECT 'Umumiy talabalar soni' AS metrika, COUNT(*)::NUMERIC AS qiymat FROM talabalar
UNION ALL
SELECT 'Faol fanlar soni', COUNT(*)::NUMERIC FROM fanlar
UNION ALL
SELECT 'Tizimdagi jami baholar soni', COUNT(*)::NUMERIC FROM baholar
UNION ALL
SELECT 'Maktab bo_yicha o_rtacha ko_rsatkich', ROUND(AVG(baho), 2) FROM baholar
UNION ALL
SELECT 'Bahosiz qolgan talabalar soni', COUNT(DISTINCT t.id)::NUMERIC
FROM talabalar t
LEFT JOIN baholar b ON t.id = b.talaba_id
WHERE b.baho IS NULL;
