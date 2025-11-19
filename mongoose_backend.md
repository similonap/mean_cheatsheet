# How to create a nest backend with mongoose

## Create a new NestJS project
```bash
npm i -g @nestjs/cli
nest new my-nest-mongo-app
cd my-nest-mongo-app
```

Vergeet niet CORS in te schakelen via een configuratie in main.ts:

```ts
const app = await NestFactory.create(AppModule, { cors: true });
await app.listen(3000); // of andere poort
```

## Install Mongoose packages
```bash
npm install @nestjs/mongoose mongoose
```

## Configure database connection (src/app.module.ts)
```ts
import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';

@Module({
  imports: [
    MongooseModule.forRoot('mongodb://localhost:27017/nest-demo'),
  ],
})
export class AppModule {}
```

## Een module, service en controller genereren

Als we bijvoorbeeld een resource "items" willen maken, kunnen we de volgende commando's gebruiken:

```bash
nest g module items
nest g service items
nest g controller items
```


<!-- import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { HydratedDocument } from 'mongoose';

export type EntryDocument = HydratedDocument<Entry>;

@Schema({ collection: 'entries' })
export class Entry {
  @Prop({ type: Number, required: true, unique: true })
  id!: number;

  @Prop({ type: String, required: true })
  date!: string;

  @Prop({ type: String, required: true })
  runType!: string;

  @Prop({ type: Number, required: true })
  actualDistanceKm!: number;

  @Prop({ type: Number, required: true })
  actualDurationMin!: number;

  @Prop({ type: Number, required: true })
  sRPE!: number;

  @Prop({ type: String, required: false })
  notes?: string;
}

export const EntrySchema = SchemaFactory.createForClass(Entry);

 -->

## Create schema (src/items/schemas/item.schema.ts)

```ts
import { Prop, Schema, HydratedDocument } from '@nestjs/mongoose';
import { SchemaFactory } from '@nestjs/mongoose';

export type ItemDocument = HydratedDocument<Item>;

@Schema()
export class Item {
    @Prop({ type: Number, required: true, unique: true })
    id!: number;
    @Prop({ type: String, required: false })
    prop1: string; 

    @Prop({ type: Number, required: true })
    prop2: number;

    @Prop({ type: Boolean, required: true })
    prop3: boolean;
}

export const ItemSchema = SchemaFactory.createForClass(Item);
```

## EntriesModule toevoegen aan AppModule (src/app.module.ts)

```ts
import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';
import { ItemsModule } from './items/items.module';

@Module({
  imports: [
    MongooseModule.forRoot('mongodb://localhost:27017/nest-demo'),
    ItemsModule,
  ],
})
export class AppModule {}
```

## Register schema in module (src/items/items.module.ts)

```ts
import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';
import { ItemsService } from './items.service';
import { ItemsController } from './items.controller';
import { Item, ItemSchema } from './schemas/item.schema';

@Module({
  imports: [
    MongooseModule.forFeature([{ name: Item.name, schema: ItemSchema }]),
  ],
  controllers: [ItemsController],
  providers: [ItemsService],
})
export class ItemsModule {}
```

## DTO's maken voor create en update (src/items/dto/create-item.dto.ts en src/items/dto/update-item.dto.ts)

```ts
export interface CreateItemDto {
    id: number;
    prop1: string;
    prop2: number;
    prop3: boolean;
}
```

```ts
export type UpdateItemDto = Partial<CreateItemDto>;
```

## Service implementeren (src/items/items.service.ts)

```ts
import { Injectable, NotFoundException } from '@nestjs/common';
import { InjectModel } from '@nestjs/mongoose';
import { Model } from 'mongoose';
import { Item, ItemDocument } from './schemas/item.schema';
import { CreateItemDto } from './dto/create-item.dto';

@Injectable()
export class ItemsService {
  constructor(@InjectModel(Item.name) private itemModel: Model<ItemDocument>) {}

    async create(dto: CreateItemDto): Promise<Item> {
        return new this.itemModel(dto).save();
    }

    async findAll(): Promise<Item[]> {
        return this.itemModel.find().exec();
    }

    async findOne(id: number): Promise<Item | null> {
        const entry = this.itemModel.findOne({ id }).lean().exec();
        if (!entry) throw new NotFoundException('Item not found');
        return entry;   
    }

    async remove(id: number): Promise<void> {
        const res = await this.itemModel.deleteOne({ id }).exec();
        if (res.deletedCount === 0) {
            throw new NotFoundException('Item not found');
        }
    }

    async update(id: number, payload: Partial<Item>): Promise<Item> {
        const updated = await this.itemModel.findOneAndUpdate(
            { id },
            { $set: { ...payload, id } },
            { new: true }
        ).lean().exec();
        if (!updated) {
            throw new NotFoundException('Item not found');
        }
        return updated as Item;
    }
}
```

Je kan eventueel ook filters toevoegen aan de findAll methode, bijvoorbeeld:

```ts
    async findAll(filter: Partial<Item> = {}): Promise<Item[]> {
        return this.itemModel.find(filter).exec();
    }
```

Of via een regex als je op strings wil filteren:

```ts
    async findAll(filter: Partial<Item> = {}): Promise<Item[]> {
        const mongooseFilter: FilterQuery<Partial<Item>> = {};

        if (filter.prop1) {
            mongooseFilter.prop1 = new RegExp(`^${filter.prop1}$`, 'i');
        }
        if (filter.prop2 !== undefined) {
            mongooseFilter.prop2 = filter.prop2;
        }
        if (filter.prop3 !== undefined) {
            mongooseFilter.prop3 = filter.prop3;
        }

        return this.itemModel.find(mongooseFilter).exec();
    }
```

## Controller implementeren (src/items/items.controller.ts)

```ts
import { Controller, Delete, Get, Param, ParseIntPipe, Post, Query, Body, Put } from '@nestjs/common';
import { ItemsService } from './items.service';
import { CreateItemDto } from './dto/create-item.dto';
@Controller('items')
export class ItemsController {
  constructor(private readonly itemsService: ItemsService) {}

    @Get()
    findAll(@Query() query: Partial<CreateItemDto>) {
        return this.itemsService.findAll(query);
    }

    @Get(':id')
    findOne(@Param('id', ParseIntPipe) id: number) {
        return this.itemsService.findOne(id);
    }

    @Post()
    create(@Body() dto: CreateItemDto) {
        return this.itemsService.create(dto);
    }

    @Delete(':id')
    remove(@Param('id', ParseIntPipe) id: number) {
        return this.itemsService.remove(id);
    }

    @Put(':id')
    update(@Param('id', ParseIntPipe) id: number, @Body() body) {
        return this.itemsService.update(id, body);
    }
}
```

## Schema met array property

```ts
@Prop({ type: [String], required: true })
tags: string[];
```

## Schema met geneste subdocumenten

```ts
@Schema()
export class SubDocument {
    @Prop({ type: String, required: true })
    subProp1: string;

    @Prop({ type: Number, required: true })
    subProp2: number;

}

export const SubDocumentSchema = SchemaFactory.createForClass(SubDocument);
```

```ts
@Schema()
export class ItemWithSubDocs {
    @Prop({ type: [SubDocumentSchema], required: true })
    subDocs: SubDocument[];
}

export const ItemWithSubDocsSchema = SchemaFactory.createForClass(ItemWithSubDocs);
```