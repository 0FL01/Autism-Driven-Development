Проанализируй текущий план/задачу и создай `IMPLEMENTATION_BLUEPRINT.md` в корне репозитория. 

**Твоя главная задача:** гарантировать, что каждая фаза реализации опирается на актуальную документацию. Ты должен планировать действия так, чтобы агент-исполнитель не галлюцинировал API, а проверял их.

Разбей реализацию на автономные фазы (Phases). Для каждой фазы обязательно укажи:

1.  **Goal**: Краткая цель фазы.
2.  **Resource Context**: 
    - 📄 **Files**: Список локальных файлов проекта.
    - 📚 **Crate Documentation**: Список путей в `docs.rs` или конкретных элементов (struct/trait), которые необходимо изучить через `mcp-rust-docs` перед написанием кода.
3.  **Steps**: Пронумерованный список конкретных технических действий. 
    - **ОБЯЗАТЕЛЬНО:** Первым шагом в каждой фазе, где используется внешний API или сложная системная логика, должен идти шаг верификации.

#### 🛡 ПРАВИЛА ВЕРИФИКАЦИИ (ОБЯЗАТЕЛЬНО К ИСПОЛНЕНИЮ)
- **Новые зависимости:** Если фаза включает использование новой зависимости — включи шаг `search_crate` и `search_documentation_items` для проверки фич и версий.
- **Сложные Trait'ы:** Если фаза включает реализацию трейта (напр. `FromRequest`, `Serialize`, `Future`) — включи шаг чтения документации этого трейта через `retrieve_documentation_page`.
- **Breaking Changes:** Если используется популярный крейт (напр. `tokio`, `axum`, `serde`), всегда планируй проверку сигнатур методов, так как они часто меняются.
- **Инструментарий:** Использование `cargo-check` и `cargo-clippy` должно быть финальным этапом каждой фазы.

#### 🛡 FUTURE-PROOFING (СОХРАННОСТЬ КОДА)
- **Запрет на удаление:** ЗАПРЕЩЕНО удалять скелеты функций, структуры или импорты, которые помечены как "для будущих фаз" в blueprint.
- **Подавление ворнингов:** Если `cargo-check`/`clippy` ругаются на неиспользуемый код (dead code), ты должен добавить `#[allow(dead_code)]` или `#[allow(unused)]` вместо удаления кода.
- **Заглушки:** Используй `todo!()` внутри функций, которые будут реализованы в следующих фазах, чтобы удовлетворить проверку типов.

Соблюдай структуру и визуальный стиль из примера (чекбоксы ✅, иконки 📄, блоки [!NOTE]). Пиши на языке проекта (английский для кода/структуры).

---

### Пример структуры IMPLEMENTATION_BLUEPRINT.md

## Phase 1: Networking Layer & Stream Processing ✅

**Goal**: Implement an asynchronous TCP listener with custom frame decoding.

**Resource Context**:
- 📄 `src/main.rs`
- 📄 `src/config.rs`
- 📚 **Docs**: `tokio::net::TcpListener`, `tokio_util::codec::FramedRead`, `futures::StreamExt`

**Steps**:
1. [ ] **Verify API**: Use `search_documentation_items` for `tokio_util::codec` to confirm `LinesCodec` vs custom `Decoder` traits.
2. [ ] **Validate Signatures**: Use `retrieve_documentation_page` for `TcpListener::accept` to check the returned `SocketAddr` handling in the current Tokio version.
3. [ ] **Infrastructure**: Add `tokio-util` with `codec` feature using `cargo-add`.
4. [ ] **Implementation**: Implement the listener loop in `src/server.rs`.
5. [ ] **QA**: Run `cargo-check --package core-server` to verify types.

[!IMPORTANT]
Before implementing the `Decoder` trait, retrieve the documentation for `tokio_util::codec::Decoder` to ensure `decode` and `decode_eof` signatures match the latest docs.rs.

[!NOTE]
Check `workspace-info` to ensure `tokio` version consistency across the workspace.

## Phase 2: Credit Ledger Logic ✅

**Goal**: Implement the transaction processing engine.

**Resource Context**:
- 📄 `src/ledger.rs`
- 📚 **Docs**: `sqlx::Transaction`, `rust_decimal::Decimal`

**🛡 Invariant Check (Safety Bounds)**:
1. **Balance Integrity**: Total sum of all entries in a transaction must ALWAYS equal zero (double-entry bookkeeping).
2. **No Overflow**: Use `checked_add` / `checked_sub`. The agent MUST NOT use wrapping arithmetic.
3. **Atomicity**: The database connection must not be returned to the pool until the transaction is explicitly committed or rolled back.

**Steps**:
1. [ ] **Verify**: Check `rust_decimal` docs for `checked` operations.
2. ...
